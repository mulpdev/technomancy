
```
(target-triplet)-objdump -b binary -m (target arch from objdump -i) --endian={little|big} -M hex --adjust-vma=0xabcd1000 -D file.bin
```