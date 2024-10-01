```
sudo apt install openjdk-17-jdk
```
# Username
Edit `{GHIDRA_DIR}/support/launch.properties` and append

```
VMARGS=-Duser.name=username
```

# RAM
Edit `{GHIDRA_DIR}/ghidraRun`

uncomment `MAXMEM-2G` and set to something higher

# HiDPI scaling
https://gist.github.com/nstarke/baa031e0cab64a608c9bd77d73c50fc6
in `$GHIDRA_ROOT/support/launch.properties` modify this line
```
VMARGS_LINUX=-Dsun.java2d.uiScale=1

VMARGS_LINUX=-Dsun.java2d.uiScale=2
```