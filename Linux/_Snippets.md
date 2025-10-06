
print history without line numbers
- history uses first 7 columns for number
```sh
history | cut -c 8-
```

kill all process matching a name
```sh
kill -9 $(ps aux | awk '{if (match($11, "procname")) print $2;}')

# example
kill -9 $(ps aux | awk '{if (match($11, "qemu")) print $2;}')
```

run `cmd` on every match
```sh
cmd $(find . -name "filename" 2>/dev/null | grep pattern)
```

Find all files NOT starting with `test*.c` and grep for pattern
```sh
grep -in "pattern" $(find . -type f ! -name "test*.c")
```

Escape space in path names and then use path as pdfgrep argument
```sh
sed -e 's/ /\\ /g' ~/filename.ext | xargs pdfgrep -in 'protocol' {} \;
```

Get IPv4 address of interface eth0
```sh
ip -br addr show eth0 | awk '{print $3}'
```

tree but limit depth
- `-L` must have space before number or "Invalid level, must be greater than 0."
```sh
tree -L 3 path/to/folder
```

list files by date
```sh
ls -t # descending, newest-first
ls -tr # "-r reverse", ascending, oldest first

```



# socket to fd
PTR is pointer to sockaddr_in
- port is 2 bytes into it

`uint16_t a = ((uint8_t) *(PTR+2) << 8)) + ((uint8_t) *(PTR+3) << 8))`

print as hex or decimal


# FDs
3 ways
```
ss -mlpu | grep <procname>

ls -la /proc/pid/fd

lsof -i -a -P -p <pid>
```