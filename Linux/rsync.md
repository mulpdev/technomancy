
```sh
rsync -a --info-progress --info=progress2 src/path dst/path

sudo rsync -PLaz ./mymnt/* destfolder/


```

-`src/path/*` recursively copy data 
-`src/path` recursively copy data starting with path dir

-`dst/path` place data into path dir

- `--archive, -a` archive mode is -rlptgoD (no -A -X -U -N -H)
	- (NO) -A copy acls
	- (NO) -X copy xattr
- `-c` checksum instead of timestamps
- `-h` human readable numbers
- `--progress, --info=progress` progress bar for current fle
- `--info=progress2` progress bar for all files
- `-P` same as --partial --progress 
	- <font color="#FF1493"> (if xfer aborted, don't need to start over)</font>
	- `-P` does spam output with filenames, use just --progress to avoid
- `--copy-links, -L` transform symlink into referent file/dir <font color="#FF1493"> (ifor copying from container/system install</font>
- `--compress, -z` compress file data during transfer
- `--delete` <font color="#FF1493"> Delete anything dest that doesn't match src, ex sync/backup dirs</font>
- `-v` verbose
- `-x` don't cross filesystem boundaries