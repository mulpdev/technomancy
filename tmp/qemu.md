```
# make hd
qemu-img create -f qcow2 my-hdd.qcow2 60G

# Boot ISO to install system
qemu-system-x86_64 -cdrom ./some.iso -m 8192 -drive file=my-hdd.qcow2

# Boot VM with port forwarding of SSH to guest ``
qemu-system-x86_64 -m 8192 -drive file=my-hdd.qcow2 -nic user,hostfwd=::tcp2222-:22
ssh user@127.0.0.1 -p 2222

#with multicore
qemu-system-x86_64 -m 8192 -smp 3 -drive file=my-hdd.qcow2
```