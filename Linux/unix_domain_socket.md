# Find remote side of a unix domain socket
Requires netlink api, kernel 3.3+ w/ DIAG

0. (OPTIONAL) Pull fd number of unix socket in question via gdb/ptrace and use /proc/pid/fd/ to identify it's inode
	1. socket:[12345]
1. Use ss to dump unix sockets and grep for pid or program name (or socket inode from step 0) and it will tell you the remote inode
	1. `ss -axp | grep -i name/pid`
2. Grep for remote inode in ss output
	1. `ss -axp | grep -i 12345`

The same thing applies to unix domain sockets of type datagram. Sudo has multiple sockets connected so a single socket in journald


# GDB hacky

`lsof -U` to get kernel address

gdb /usr/src/linux/vmlinux /proc/kcore

`(gdb) p ((struct unix_sock*)0xPOINTER)->peer`