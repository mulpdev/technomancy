df - disk filesystem
```sh
$ df -h    
Filesystem        Size  Used Avail Use% Mounted on  
udev              7.8G     0  7.8G   0% /dev  
tmpfs             1.6G  1.6M  1.6G   1% /run  
/dev/sda2         457G  156G  278G  36% /  
tmpfs             7.8G   42M  7.8G   1% /dev/shm  
tmpfs             5.0M   12K  5.0M   1% /run/lock  
/dev/sda1         511M  5.9M  506M   2% /boot/efi  
tmpfs             1.6G   64K  1.6G   1% /run/user/1000
```

du - disk usage of all directories in {dir}
- `-d` depth
```sh
$ du -h {dir}
24K     ./.cache/mesa_shader_cache/2a  
24K     ./.cache/mesa_shader_cache/bf
...SNIP...
```