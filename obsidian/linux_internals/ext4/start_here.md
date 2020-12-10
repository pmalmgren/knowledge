## ext4

ext4 is a filesystem for Linux which is directly incorporated into the Linux kernel source. It lives under the fs/ext4 directory.

### file_system_type

The `file_system_type`, which is a high level overview of the filesystem, lives in [fs/ext4/super.c](https://github.com/torvalds/linux/blob/v5.8/fs/ext4/super.c#L6262).

### super block

The super block is the most important part of a filesystem. It's stored as one of the first blocks on disk, and contains metadata about disk information. For ext4, this is defined as [ext4_super_block](https://github.com/torvalds/linux/blob/v5.8/fs/ext4/ext4.h#L1245).

### super_operations

The `super_operations` for ext4 is defined in [fs/ext4/super.c](https://github.com/torvalds/linux/blob/v5.8/fs/ext4/super.c#L1471). 

### inode_operations

The `inode_operations` structure describes how the VFS can manipulate an inode on your filesystem. For `ext4` these are defined in [fs/ext4/file.c](https://github.com/torvalds/linux/blob/v5.8/fs/ext4/file.c#L904).

### address_space_operations

The `address_space_operations` handle pages (not blocks) and are used by the page cache in Linux. There are different ones for each 

### references

- https://github.com/torvalds/linux/blob/v5.8/Documentation/filesystems/vfs.rst#L1
- 