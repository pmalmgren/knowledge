## How Linux Looks Up Directories In open()

### struct name_idata

First, the `name_idata` struct is initialized. The purpose of the `name_idata` struct, which is defined in [linux/namei.c](https://github.com/torvalds/linux/blob/v5.8/fs/namei.c#L502) is to store the current task status of path walking. It maps names to inodes, hence the "name_idata".

The struct contains several fields, the most important being:

- `struct path path` which is a pair of `vfsmount` (refers to the and `dentry`, containing the current status of the walk from the `dcache`)
- `qstr last` which contains the next component of the pathname
- `int last_type` which contains information about the next component, `NORM` meaning a file, `ROOT` meaning the filesystem "/", `DOT` meaning ".", and ".." `DOTDOT`
- `struct path root` which references the root of the filesystem (does this change with relative pathnames?)

### link_path_walk

`link_path_walk` walks each component of a path to ensure that it exists. It does this by calling `walk_component` with the current `name_idata` that contains the status of the walk.

If the component is a `LAST_NORM` (see `last_type` field of the `name_idata` struct) then tries to lookup the path from either the `dcache` or the filesystem.


### walk_component

`walk_component` calculates a hash for the `dentry` and looks it up in the `dcache` using `lookup_fast`, if it misses then `inode->i_op->lookup(dentry, inode, flags)` is called.

For `ext4` this is defined in [fs/ext4/namei.c](https://github.com/torvalds/linux/blob/v5.8/fs/ext4/namei.c#L1682).

### do_open

After checking permissions, flags, etc. the final step is to call `vfs_open` with the walked `name_idata` structure and the file that was opened.

`vfs_open` calls `do_dentry_open`, which calls `file_operations.open` on the file and the inode. `open` is implemented by the filesystem I believe.

For ext4, this would be `ext4_file_open`

## bfptrace script

Here's a script which traces walks from `do_filp_open` all the way down to `ext4_open`. 

```
#!/usr/bin/bpftrace

#include <linux/fs.h>
#include <linux/namei.h>
#include <linux/path.h>
#include <linux/dcache.h>

struct nameidata {
	struct path	path;
	struct qstr	last;
	struct path	root;
	struct inode	*inode; /* path.dentry.d_inode */
	unsigned int	flags;
	unsigned	seq, m_seq, r_seq;
	int		last_type;
	unsigned	depth;
	int		total_link_count;
	struct saved {
		struct path link;
		struct delayed_call done;
		const char *name;
		unsigned seq;
	} *stack, internal[2];
	struct filename	*name;
	struct nameidata *saved;
	unsigned	root_seq;
	int		dfd;
	kuid_t		dir_uid;
	umode_t		dir_mode;
};

BEGIN 
{
	printf("Tracing do_filp_open calls from %d\n", $1);
}

kprobe:do_filp_open /pid == $1/ 
{
	$filename = str(((struct filename *)arg1)->uptr);
	printf("do_filp_open(dfd=%d, filename->name=%s)\n", arg0, $filename);
}

kprobe:link_path_walk.part.0 /pid == $1/
{
	$filename = str(arg0);
	printf("link_path_walk(filename=%s)\n", $filename);
}

kprobe:walk_component /pid == $1/
{
	$filename = str(((struct nameidata *)arg0)->path.dentry->d_name.name);
	$arg = "DONE";
	if (arg1 == WALK_TRAILING) {
		$arg = "WALK_TRAILING";
	}
	if (arg1 == WALK_MORE) {
		$arg = "WALK_MORE";
	}
	if (arg1 == WALK_NOFOLLOW) {
		$arg = "WALK_NOFOLLOW";
	}
	printf("walk_component(nd->path=%s, flags=%s)\n", $filename, $arg);
}

kprobe:ext4_lookup /pid == $1/
{
	$filename = str(((struct dentry *)arg1)->d_name.name);
	printf("dcache_miss: ext4_lookup called on %s\n", $filename);
}

kprobe:ext4_file_open /pid ==$1/
{
	$file = str(((struct file *)arg1)->f_path.dentry->d_name.name);
	printf("ext4_file_open called on %s\n", $file);
}
```

## References

- [Path Lookup Kernel Documentation](https://www.kernel.org/doc/html/latest/filesystems/path-lookup.html)

## Tags

#linux #linux-kernel #files 