Imhex instead of HxD for RE

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

n-day diffing CVE-2024-21338 is a good first n-day to get working

Objdump disassemble flat file (not ELF/PE)
```
(target-triplet)-objdump -b binary -m (target arch from objdump -i) --endian={little|big} -M hex --adjust-vma=0xabcd1000 -D file.bin
```