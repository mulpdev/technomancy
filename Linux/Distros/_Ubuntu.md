
```
sudo apt -o Acquire::Check-Valid-Until=false install/update
```

from https://stackoverflow.com/questions/22008193/how-to-list-download-the-recursive-dependencies-of-a-debian-package

# Recursively list dependencies
pkgname=$1

apt-cache depends --recurse --no-recommends --no-suggests --no-conflicts --no-breaks --no-replaces --no-enhances $pkgname | grep "^\w" | sort -u

# Download those dependencies
apt-get download $(apt-cache depends --recurse --no-recommends --no-suggests --no-conflicts --no-breaks --no-replaces --no-enhances $pkgname | grep "^\w" | sort -u)

# Install 1: Create index
```sh
dpkg-scanpackages . | gzip -9c > Packages.gz
```


# Install 2: Set folder as apt source and install
```sh
echo "deb file:<your folder here> ./" | sudo tee -a /etc/apt/sources.list

sudo apt-get update
```


# Recursive dependencies of .deb
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

# Isolated Network Ubuntu
# VARIABLES
`<LOCALDOMAIN>` is something like `updates.domain.lan`
`<LOCALURL>` is something like `updates.domain.lan/mirrors/` or `updates.domain.lan/pypi/`

# Install cert to system (for apt, etc)
- Remainder of cert assumes this was done first and file is already downloaded
- WEB BROWSERS require manual install, just point it to downloaded file

```
# initial install certs
sudo apt install wget
sudo wget --no-check-certificate -O /usr/local/share/ca-certificates/FILENAME URL
<repeat if multiple certs>
sudo update ca-certificates
```

# Configure Apt
## Point to self-hosted mirrors
```
sudo sed -i 's@archive.ubuntu.com/@<LOCALURL>/@g' /etc/apt/sources.list
sudo sed -i 's@security.ubuntu.com/@<LOCALURL>/@g' /etc/apt/sources.list
```

## Allow insecure repos
```
sudo tee -a /etc/apt/apt.conf.d/10no--check-valid-until > /dev/null <<EOT
Acquire::Check-Valid-Until "0";
Acquire::AllowInsecureRepositories "true";
EOT
```

# Pypi
## temporary
`python3 -m pip install BLAH --trusted-host <LOCALDOMAIN>

## Permenant
```
sudo apt install python3-pip

# FOR USER
mkdir ~/.config/pip
cat > ~/.config/pip/pip.conf << EOF
[global]
cert=/usr/local/share/ca-certificates/FILENAME
index-url=<LOCALURL>
trusted-host=<LOCALDOMAIN>
EOF

cp ~/.config/pip/pip.conf ~/.config/pip/pip3.conf

# FOR SYSTEM
sudo cp ~/.config/pip/pip.conf /etc/pip.conf
sudo chmod 644 /etc/pip.conf
sudo python3 -mpip install --upgrade pip
```

# CIFS mount

## LDAP creds
- from fstab line `credentials=/etc/cred/ldap`

```
sudo mkdir -p /etc/cred/
sudo touch /etc/cred/ldap
sudo ./create_ldap_creds
```

```sh create_ldap_creds
### Below won't work on copy/paste, it MUST be run as a shell script

# Securely enter user/pass
stty -echo # Don't echo typing
trap 'stty echo' EXIT # reset in case script dies before re-enabling
printf "LDAP username:\n"
read LDAP_USERNAME
printf "LDAP password:\n"
read LDAP_PASSWORD
stty echo
printf '\n'

# Make the file
sudo tee -a /etc/cred/ldap > /dev/null <<EOT
username=$LDAP_USERNAME
domain=<DOMAIN>
password=$LDAP_PASSWORD
EOT

unset LDAP_PASSWORD
unset LDAP_USERNAME
### Above won't work on copy/paste, it MUST be run as a shell script
```

## Mount
```
sudo apt install cifs-utils

sudo mkdir -p /mnt/files/<mnt_folder>
sudo chmod 755 -R /mnt/files/

sudo tee -a /etc/fstab > /dev/null <<EOT
//<URL>/<FOLDER> /mnt/files/<mnt_folder> cifs noauto,cache=none,credentials=/etc/cred/ldap,noperm,file_mode=0664,dir_mode=0755 0 0
```

# change to KDE
```
sudo apt-get purge --auto-remove ubuntu-gnome-desktop gnome-shell
sudo apt autoremove
sudo apt install kde-plasma-desktop
sudo reboot 0
```

# SSH
```
sudo apt install openssh-server
sudo systemctl enable ssh

ssh-keygen -t ed25519 -C "user@domain"
ssh-copy-id user@remote
```

# pwndbg and Ubuntu 20.04 if repos from Aug 2022
Modified requirements.txt to be dropped into pwndbg repo root to allow ./setup.sh to complete and gdb to run

```
capstone==4.0.2
psutil==5.9.1
pwntools==4.9.0b0
pycparser==2.21
pyelftools==0.29
Pygments==2.12.0; python_version < '3.7'
Pygments==2.12.0; python_version >= '3.7'
ROPGadget==6.8
tabulate==0.8.10; python_version < '3.7'
tabulate==0.8.10; python_version >= '3.7'
typing-extensions==4.3.0; python_version >= '3.7'
typing-extensions==4.1.1; python_version < '3.7'
unicorn==2.0.0; python_version >= '3.7'
unicorn==2.0.0rc7; python_version < '3.7'
requests==2.20.0
```