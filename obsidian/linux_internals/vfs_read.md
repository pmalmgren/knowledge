## vfs_read

vfs_read is the main entrypoint of the `read` systemcall and does the following:

- verifies bounds and that the caller is able to read the file via `rw_verify_area`
- calls [[security_file_permission]] to determine if the task has permissions to read from the file
- calls `file->f_op->read` which passes through to the underlying filesystem

## tags

#linux-kernel #filesystem #vfs 