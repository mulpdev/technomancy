# For everything in a directory
`--unified` Diff format is unified context
`--strip` strips this many slashes in the path. MUST BE SAME NUMBER IN BOTH DIRS
`--posix` Apply patches in POSIX compliant way
`--set-utc` Update timestamps
`--verbose` Explain everything
```
diff --unified --recursive --no-dereference OG_DIR/ MOD_DIR/ > patch.diff

# Test with dry-run on the fresh directory
patch --dry-run --directory=OG_DIR/ --unified --strip=1 --posix --set-utc --verbose < patch.diff

# Apply
patch --dry-run --directory=MOD_DIR/ --unified --strip=1 --posix --set-utc --verbose < patch.diff
```

https://unix.stackexchange.com/questions/162131/is-this-a-good-way-to-create-a-patch
```
1. To create patch for single file your command may look like
    
    `diff -Naru file_original file_updated > file.patch`
    
    where
    
    - `-N`: treat absent files as empty
    - `-a`: treat all files as text
    - `-r`: recursively compare any subdirectories found
    - `-u`: output NUM (default 3) lines of unified context
2. To create patch for whole directory:
    
    `diff -crB dir_original dir_updated > dfile.patch`
    
    where
    
    - `-c`: output NUM (default 3) lines of copied context
    - `-r`: recursively compare any subdirectories
    - `-B`: ignore changes whose lines are all blank

After all to apply this patch one can run

```
patch -p1 --dry-run < dfile.patch
```

where switch `p` instructs patch to strip the path prefix so that files will be identified correctly. In most cases it should be `1`.

Remove `--dry-run` if you are happy from the result printed on the screen.
```