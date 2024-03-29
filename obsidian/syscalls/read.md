## read

read attempts to read up to `count` bytes from a given file descriptor into a buffer. on error, `read()` returns -1 and sets `errno` appropriately

	read() attempts to read up to count bytes from file descriptor fd into the buffer starting at buf.

	On files that support seeking, the read operation commences at the file offset, and the file offset is incremented by the number of bytes read.  If the file offset is at or past the end of file, no bytes are read, and read() returns zero.

	If count is zero, read() may detect the errors described below.  In the absence of any errors, or if read() does not check for errors, a read() with a count of 0 returns zero and has no other effects.

	According to POSIX.1, if count is greater than SSIZE_MAX, the result is implementation-defined; see NOTES for the upper limit on Linux.

## tags

#linux #systemcall #io