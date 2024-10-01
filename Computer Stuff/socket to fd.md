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