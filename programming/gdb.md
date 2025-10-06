# gdb server

Target runs `gdbserver`
```sh
gdbserver ip:port /path/to/binary
```

Host runs `gdb`, `{triplet}-gdb`, OR `gdb-multiarch` and connects to remote target gdbserver
- If breaking in, `(gdb) cont` should avoid the Inferior Process error

```sh
gdb -ex 'target remote ip:port' ./a.out
# OR
(gdb) target remote ip:port
```
# gdbinit

Create a user one at `~/.config/gdb/gdbinit` or `~/.gdbinit`

To enable loading/running scripts (not system/user wide) the user gdbinit must exist and contain one or more  `auto-load safe-path` directives of trusted directories
- sub-directories under path are also included as trusted
- multiple directories can be separate directives or combined with `:` (like PATH)
```
set auto-load safe-path path/to/folder1
set auto-load safe-path path/to/folder2

set auto-load safe-path path/to/folder1:path/to/folder2
```

- For a single GDB session, you can use the `-iex` option to set the `auto-load safe-path` at startup:
```sh
gdb -iex "set auto-load safe-path /path/to/folder1:/path/to/folder2" path/to/bin
```

# pwndebug

# gef