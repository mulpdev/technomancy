
# ~/./local/ bin,include,lib,share, 
https://lemmy.zip/post/37449272
- systemd `~/.local/bin`  is supposedly for user scripts?

https://lemmy.zip/post/37449272/18401781
```
Akatsuki Levi
@lemmy.world

I use ~/.local/bin since by linux standard, ~/.local is a user-level /usr/local, which is a override level of /usr

~/bin ends up cluttering the home folder
```
https://lemmy.zip/post/37449272/18403503
```
Another reason to use ~/.local is you can do things like

./configure --prefix=$HOME/.local
make -j$(ncpu)
make install

And then you get your .local/bin, .local/share, .local/include, .local/lib and such, just like /usr but scoped to your user.
```
# mount

Ubuntu ISO live dvd mount a vmware drive attached
```
sudo su # default creds are ubuntu:ubuntu
mkdir /mnt/q
mount /dev/sdXY /mnt/q
```
# ssh
https://www.reddit.com/r/archlinux/comments/1c1bog3/do_you_install_and_use_fail2ban/
```sh
sudo apt install openssh-server
systemctl start ssh
#edit /etc/ssh/sshd_config and uncomment "PermitRootLogin" and set to yes
systemctl daemon-reload # or maybe systemctl restart ssh
```
# sudo
/etc/sudoers

No password sudo, Paste line AFTER sudo group if user in it
```
username ALL=(ALL) NOPASSWD: ALL`
```

Manually reset password
```sh
# /etc/passwd
openssl passwd -HASHID -salt 12345678 newpassword

# /etc/shadow <username>:$<hashtype>$<salt>$<hash>:(rest of line)
openssl passwd -<hashtype> -salt <NEW salt> <new password>
```

