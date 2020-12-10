## ksys_read

`ksys_read` is the defined entrypoint for the [[read]] systemcall, defined in [fs/read_write.c](https://github.com/torvalds/linux/blob/v5.8/fs/read_write.c#L596)

It does the following things:

- gets the file matching the file descriptor passed in as an argument with `fdget_pos`
- gets the offset of the file and copies it
- calls [[vfs_read]] with the file, userspace buffer, bytes to read into it, and position of the file
- writes the offset 

## tags

#linux-kernel #io