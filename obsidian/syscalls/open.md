## Open System Call

open asks the kernel to open a file, specified at pathname, and returns an error or a file descriptor.

from the man page:

       The  open()  system call opens the file specified by pathname.  If the specified file does not exist, it may optionally (if O_CREAT is specified in flags) be created by open().

       The return value of open() is a file descriptor, a small, nonnegative integer that is used in subsequent system  calls  (read(2),  write(2),  lseek(2),  fcntl(2), etc.)  to  refer  to  the  open  file.   The  file descriptor returned by a successful call will be the lowest-numbered file descriptor not currently open for the process.
	   
 because of the universality of I/O, the open system call can be used to open a pipe, FIFO, message queue, or a socket
 
 ## tags
 
 #open #systemcall #linux #unix