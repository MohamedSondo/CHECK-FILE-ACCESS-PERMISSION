# CHECK-FILE-ACCESS-PERMISSION
check read write exe permission on file
The access system call determines whether the calling process has access permission to a file. It can check any combination of read, write, and execute permission, and it can also check for a file's existence.

The access call takes two arguments. The first is the path to the file to check. The second is a bitwise or of R_OK, W_OK, and X_OK, corresponding to read, write, and execute permission. The return value is 0 if the process has all the specified permissions. If the file exists but the calling process does not have the specified permissions, access returns –1 and sets errno to EACCES (or EROFS, if write permission was requested for a file on a read-only file system).

If the second argument is F_OK, access simply checks for the file's existence. If the file exists, the return value is 0; if not, the return value is –1 and errno is set to ENOENT. Note that errno may instead be set to EACCES if a directory in the file path is inaccessible.

The program shown in Listing 8.1 uses access to check for a file's existence and to determine read and write permissions. Specify the name of the file to check on the command line.
Listing 8.1 (check-access.c) Check File Access Permissions





#include <errno.h>
#include <stdio.h>
#include <unistd.h>

int main (int argc, char* argv[])
{
 char* path = argv[1];
 int rval;

 /* Check file existence. */
 rval = access (path, F_OK);
 if (rval == 0) 
  printf ("%s exists\n", path);
 else {
  if (errno == ENOENT) 
   printf ("%s does not exist\n", path);
  else if (errno == EACCES) 
   printf ("%s is not accessible\n", path);
  return 0;
 }

 /* Check read access. */
 rval = access (path, R_OK);
 if (rval == 0)
  printf ("%s is readable\n", path);
 else
  printf ("%s is not readable (access denied)\n", path);

 /* Check write access. */
 rval = access (path, W_OK);
 if (rval == 0)
  printf ("%s is writable\n", path);
 else if (errno == EACCES)
  printf ("%s is not writable (access denied)\n", path);
 else if (errno == EROFS)
  printf ("%s is not writable (read-only filesystem)\n", path);
 return 0;
}

For example, to check access permissions for a file named README on a CD-ROM, invoke it like this:

% ./check-access /mnt/cdrom/README
/mnt/cdrom/README exists
/mnt/cdrom/README is readable
/mnt/cdrom/README is not writable (read-only filesystem)
