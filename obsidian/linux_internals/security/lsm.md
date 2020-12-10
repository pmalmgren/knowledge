## lsm

Linux security modules are implemented as hooks. Each module registers hooks which get called during various operations (socket open, file open, file read, etc.) to determine if the caller has permissions to do these things.

we can see which security modules are loaded by examining the `/sys/kernel/security/lsm` file:

```
$ cat /sys/kernel/security/lsm
```

lockdown and capability come first, followed by minor modules (yama, etc) and then major modules (apparmor, selinux).

## references
- https://thibaut.sautereau.fr/2017/05/26/linux-security-modules-part-1/

## tags

#linux-kernel #security 