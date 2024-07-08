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