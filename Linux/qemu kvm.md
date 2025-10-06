Use mounted filesystem for images
1. edit /etc/libvirt/qemu.conf
2. uncomment "user=libvirt-qemu" and "group=libvirt-qemu" lines
3. replace with your username and save
4. systemctl restart libvirtd.service
5. https://medium.com/@octopwn/fix-qemu-kvm-doesnt-allow-using-mounted-drives-in-virt-manager-on-linux-permission-denied-error-916dba2c0f10

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

kill qemu
```
kill -9 $(ps aux | awk '{if (match($11, "qemu")) print $2;}')
```
# qemu usermode single binary
The docs say `-L` "sets the (ARCH)" elf interpreter prefix". By pointing it at the TARGET (NOT host) directory that `/lib` and it should find the libs required by the binary to run.

```sh
qemu-aarch64 /path/to/aarch64_bin
qemu-aarch64 -L /target/libs/dir/ /path/to/aarch64_bin
```


# Full system

Kernels and initd
```
/boot$: file vmlinuz-6.6.* initrd*

.boot$: file kernel* initramfs*
```

`-nographic` no graphics
`-M` machine type ("virt" is just virtualized generic arch cpu)
`-cpu` specific cpu
`-kernel` kernel image
`-append` root args, typically `root=/dev/whatever` maybe console tty stuff or special logging
`-initrd` can be initrd or initramfs

Drives require a map to a device
- `-device` can be used multiple times
	- example: an sd card
		- `-device sdhci-pci` is the bus for the sd card
		- `-device sd-card,drive=sd` is the actual sd card device
			- value of `drive=` used below in argument `-drive`
		- example a pci scsi device (generic)
			- `-device virtio-scsi-pci,id-scsi0` SCSI device bus
			- `-device scsi-hd,drive=hd` the actual device (/dev/sda?)
- `-drive`
	- `id` of a device created on command args
	- `if=none` to not pull from host devices
	- example sd card
		- `-drive if=none,id=hd,file=myfile.raw,format=raw` file containing filesystem
		- `-drive if-none,id=hd,file=myfile.raw,format=raw`
```
sudo apt install qemu-system-arm

qemu-system-aarch64 -nographic -M virt -cpu cortex-a53 -kernel /path/to/kernel*.img -initrd /path/to/initramfs -append "root=/dev/mmcblk0 console=ttyAMA0,115200" -device virtio-scsi-pci,id=scsi0 -device scsi-hd,drive=hd -drive if=none,id=hd,file=myfile.raw,format raw

qemu-system-aarch64 -nographic -M virt -cpu cortex-a53 -kernel /path/to/kernel*.img -initrd /path/to/initramfs -append "root=/dev/mmcblk0 console=ttyAMA0,115200" -device sdhci-pci -device sd-card,drive=sd -drive id=sd,if=none,format=raw,file=myfile.raw

```