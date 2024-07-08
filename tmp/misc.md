`grep --color -E "mov .+, r15" ropper.txt`

```
./gdbserver:5555 --attach `ps -A |grep chrome | grep sandbox | awk '{print $2}`

./gdb --pid=`ps aux | grep chrome | awk '{$print $2}'
``` 

```
adb forward tcp:5555 tcp:5555
adb reverse tcp:5555 tcp:5555
adb reverse --remove tcp:5555
```

```
lsblk
umount /dev/sdc1; sudo mkfs.ext3 /dev/sdc1; sudo eject /dev/sdc1
```

`sudo apt -o Acquire::Check-Valid-Until=false install/update`

`emcc -s WASM=1 -s EXPORTED_FUNCTIONS='["_a"]' --no-entry hw.c`

```py
def whathex(data)
	out = ""
	out = ",".join("{:02X}.format(c) for c in data)
	return out
```

`sudo dd bs=4M if=/path/to/iso of=/dev/sdX status=progress oflag=sync`