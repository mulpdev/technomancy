examples
```sh
watch ls
watch -n 0.1 ls
```

With color
```sh
# watch --color or watch -c
watch --color -n 0.1 {cmd with color}
watch --color -n 0.1 ls -lt --color # must specify --color for ls
```