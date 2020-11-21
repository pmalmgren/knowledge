## do_sys_openat

defined in [fs/open.c](https://github.com/torvalds/linux/blob/v5.8/fs/open.c#L1163)

handles the [[open]] system call

### what does it do?

####  Builds an open_how struct and immediately calls do_sys_openat2

## do_sys_openat2

Information about path lookup: https://github.com/torvalds/linux/blob/v5.8/Documentation/filesystems/path-lookup.rst#L1223

Information about open: https://www.win.tue.nl/~aeb/linux/vfs/trail-2.html

#### 1. builds and validates the file open flags

[build_open_flags](https://github.com/torvalds/linux/blob/v5.8/fs/open.c#L1005) validates the open flags and returns an error if any invalid flags were specified

#### 2. Validates the filename from userspace

[getname](https://github.com/torvalds/linux/blob/v5.8/fs/namei.c#L128)  allocates kernel memory and copies over the filename into a filename struct

#### 3. Allocates a file descriptor

`_alloc_fd` acquires a lock on the current `task_struct` files table, then finds the next available file descriptor

[get_unused_fd_flags](https://github.com/torvalds/linux/blob/v5.8/fs/file.c#L548) which ends up calling [__alloc_fd](https://github.com/torvalds/linux/blob/v5.8/fs/file.c#L480) which allocates a file descriptor

#### 4. Attempts to open the file

calls [do_filp_open](https://github.com/torvalds/linux/blob/v5.8/fs/namei.c#L3379) , which does the following:

- builds nameidata_nd struct with the file information,
- calls path_openat
- walks the tree, and [hopes each component is in the dentry cache](https://github.com/torvalds/linux/blob/v5.8/fs/namei.c#L3135), if not load it from the filesystem
- uses `link_path_walk` to walk the filesystem from beginning to end

validates the path by walking it, calls do_open, which calls vfs_open eventually

## tags

#linux-kernel #systemcall #files
