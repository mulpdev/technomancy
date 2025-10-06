# socat serial to network

```
socat -d -d -b1024 -v /dev/ttyS0,b115200,raw,echo=0,time=1,min=64 udp:1.2.3.4
```
- `-d -d` print fatal, error, warning, and notice messages
- `-b1024` sets blocksize
- `-v` write data to stream and stderr
- `/dev/ttyS0,b115200,raw,echo=0,time=1,min=64` serial connection
- `udp:1.2.3.4` network 