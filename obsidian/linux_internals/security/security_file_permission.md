## security_file_permission

determines if a task has access to a file.

starts by calling `call_int_hook` with the `file_permission` security_hook_list.

### lsm hooks

`file_permission` is implemented using hooks via [[lsm]]. The two that are installed for `file_permission` are:

- [apparmor_file_permission](https://github.com/torvalds/linux/blob/v5.8/security/apparmor/lsm.c#L1196)
- [selinux_file_permission](https://github.com/torvalds/linux/blob/v5.8/security/selinux/hooks.c#L7019)

`file_permission` is an `int` hook defined in [lsm_hook_defs](https://github.com/torvalds/linux/blob/v5.8/include/linux/lsm_hook_defs.h#L156).

## tags

#security #filesystem #io #linux-kernel 