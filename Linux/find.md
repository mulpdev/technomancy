```sh
# find all files of type/name
find . -name "*.pdf"

# generic command
find / -type f -executable -name "*iptables*"

# find files and delete them
find / -name "*.ext*" -delete

# find files and execute a command on the file "{}"
find / -name "*.ext*" -exec ls -la {} \; #exec cmd must end with \;
```
