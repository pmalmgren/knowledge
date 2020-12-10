## generic_permission

[generic_permission](https://github.com/torvalds/linux/blob/v5.8/fs/namei.c#L346) checks access rights for a specific inode.

called by [[directory_walk]] via `may_lookup` before attempting to walk each directory. this function checks a number of things, including:

- ACL lists
- File permission bits

It calls through to the `inode->i_op->permission` function, which asks the file system if the caller has permissions, and caches this for the lifetime of the inode.

## tags

#acl #permissions #security #linux-kernel