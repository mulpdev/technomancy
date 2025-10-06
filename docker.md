# tl;dr

```sh
# <hash/tagname> is the image hash OR the tag if available
sudo docker build .
sudo docker run -it <hash/tagname>

sudo docker container ls

sudo docker exec -it <hash/tagname> sh
sudo docker exec -i -u 0 <hash/tagname> sh

sudo docker stop <hash/tagname>
```

# Generic Install and Config

```sh
# install package

# enable docker.service or docker.socket
sudo systemctl enable docker.service # start on boot
sudo systemctl enable docker.socket  # start on connections to docker unix socket

sudo docker info
```

<font color="#ff0000">Adding a user to docker group may be a SECURITY HAZARD!</font>
- That user can launch a privileged container with access to host system

```sh
##############################################################
# USER CAN LAUNCH DOCKER PRIVILEGED AND GET ROOT PRIVS ON HOST
##############################################################
sudo usermod -aG docker $USER
```

# Dockerfile
Builds a single container from a series of docker run commands
- <font color="#FF1493">Each command == a NEW layer and bigger image</font>
## Example using Alpine
https://alpinelinux.org/releases/
https://pkgs.alpinelinux.org/packages

NOT OPTIMIZED FOR SPACE
```dockerfile
# Or alpine:3.19 for that version
FROM alpine:latest

# Testing Repo, if necessary
RUN echo "http://dl-cdn.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories
RUN apk update

# Install a package (e.g., curl) using Alpine's package manager (apk)
RUN apk add --no-cache curl

# Set the entrypoint to a simple command
ENTRYPOINT ["curl", "https://example.com"]
```

Build
```sh
sudo docker build .
---> <hash>

# OR tag it, sudo docker build -t my-alpine-app .
sudo docker build -t tagname .
---> <hash>  
Successfully built <hash>  
Successfully tagged <tagname>:latest
```

Run
```sh
sudo docker run <hash/tagname>

# -i interactive -t tty
# for shells etc
sudo docker run -it <hash/tagname>
```

# random tips

Copy docker filesystem 
```sh
sudo docker cp -L <hash>:/ ./folder # -L follows links
```

Find a docker container's shell. If it's not in the Dockerfile or config, try this:
```sh
# on the host
docker exec -it <hash> sh # or /bin/bash

# inside container, run these
echo $0

pp -p $$

ls -l /bin/sh # or whatever shell you exec above
#output will show if it's a symlink to /bin/busybox or something

busybox | head -1
```

Move docker container to another host
```sh
# or repository/imagename
docker save -o <path for generated tar file> <hash/tagname> 
docker load -i <path to image tar file>
```

Force Remove ALL images
```sh
docker rmi -f $(docker images -a -q)
```