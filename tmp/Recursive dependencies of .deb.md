
```
# from https://stackoverflow.com/questions/22008193/how-to-list-download-the-recursive-dependencies-of-a-debian-package

# Recursively list dependencies
pkgname=$1

apt-cache depends --recurse --no-recommends --no-suggests --no-conflicts --no-breaks --no-replaces --no-enhances $pkgname | grep "^\w" | sort -u

# Download those dependencies
apt-get download $(apt-cache depends --recurse --no-recommends --no-suggests --no-conflicts --no-breaks --no-replaces --no-enhances $pkgname | grep "^\w" | sort -u)

# Install 1: Create index
dpkg-scanpackages . | gzip -9c > Packages.gz

# Install 2: Set folder as apt source and install
#echo "deb file:<your folder here> ./" | sudo tee -a /etc/apt/sources.list

#sudo apt-get update[[Recursive dependencies of .deb]]
```