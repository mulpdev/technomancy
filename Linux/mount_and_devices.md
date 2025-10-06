Overwrite filesystem on device
```sh
lsblk
umount /dev/sdc1; sudo mkfs.ext3 /dev/sdc1; sudo eject /dev/sdc1
```

```sh
sudo dd bs=4M if=/path/to/iso of=/dev/sdX status=progress oflag=sync
```

# mount a blob
1. Validate filesystem in .bin and note
		`Sector Size:` (line2)
		`Start Sector` (last line, 3rd column)
```
fdisk -l test.bin
```

# All in 1
```
mkdir loopmount
sudo mount -o loop,offset=`echo $((start*size))` test.bin loopmount/
```


# Multistep

```
sudo losetup -f test.bin
sudo losetup -nl
</dev/loopX .....>

mkdir loopmount
sudo mount -o loop,offset=`echo $((start*size))` /dev/loopX loopmount/
```

# Finally
Become root and explore filesystem. Any changes you make will be reflected in the bin file.

ALWAYS UMOUNT WHEN DONE!