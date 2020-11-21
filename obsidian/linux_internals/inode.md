## inode

An inode displays metadata about a file, including the permissions, size, ownership, and filename.

The inode contains a reference back to the filesystem in `inode_operations` which can read data from the disk if necessary.

Used by [[open]] and friends.

## tags

#vfs #linux-kernel 