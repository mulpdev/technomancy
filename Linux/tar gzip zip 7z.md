# tar
- `c` or `x`:  *C*reate or e*X*tract (mutually exclusive)
- `v` : verbose mode
- `-f` or `--file` the archive name 
	- filename argument is immediately after option, or the first argument after all options
- `J` or `x`:  use xz or gzip compression algorithm (mutually exclusive)
```sh
# xz compressed tarball
tar cvfJ tarball.tar.xz file1 folder2/ 

# gzip compress tarball
tar cvfz tarball.tar.gz file1 folder2/
tarball.tar.xz -C ./output_dir/ 
```

