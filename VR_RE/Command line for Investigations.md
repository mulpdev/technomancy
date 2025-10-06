# Code

Find instances of `subprocess.run` in Python code

```
grep -A 2 -in "subprocess\.run" $(find . -type f -name "*.py")
```

# System Info

ip
```
ip (flags) -br a # name, up/down, CIDR

ip -br addr show eth0 | awk '{print $3}'
```


Show version of busybox binary
```
busybox | head -1
```