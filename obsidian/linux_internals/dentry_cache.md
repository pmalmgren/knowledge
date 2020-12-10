## dentry cache

The directory entry cache is used pretty heavily during file lookup operations, such as path walking, ex. [[directory_walk]] during the [[open]] system call.

directory entries map a name, in the member `qstr d_name` to an inode `d_inode`. If `d_inode` is `NULL` then the `dentry` is said to be [negative](https://lwn.net/Articles/814535/) and indicates that a file does not exist, but is kept in the cache.

The dcache API is defined in the header file [linux/dcache.h](https://github.com/torvalds/linux/blob/v5.8/include/linux/dcache.h). It serves as [more than just a cache](https://www.kernel.org/doc/html/latest/filesystems/path-lookup.html#more-than-just-a-cache) and can let us know [what filesystems are mounted](https://github.com/torvalds/linux/blob/v5.8/fs/mount.h#L33)

## bpftrace scripts

Here's a quick bpftrace script to see dentry cache lookups that succeeded:

```
#!/usr/bin/bpftrace

#include <linux/dcache.h>

kretprobe:lookup_fast /pid == $1/
{
	if (retval) {
		$filename = str(((struct dentry *)retval)->d_name.name);
		printf("dcache hit: lookup_fast on %s\n", $filename);
	}
}

```

Here's a quick bpftrace script to see dentry cache lookups that missed:

```
kretprobe:__lookup_slow /pid == $1/
{
	if (retval) {
		$filename = str(((struct dentry *)retval)->d_name.name);
		printf("dcache miss: lookup_slow on %s\n", $filename);
	}
}

```

## tags

#linux-kernel #filesystem #vfs #dcache