https://wiki.debian.org/DebianReleases

|**Version**|**Code name**|**Release date**|**End of life date**|**EOL [LTS](https://wiki.debian.org/LTS)**|**EOL [ELTS](https://wiki.debian.org/LTS/Extended)**|
|---|---|---|---|---|---|
|15|[Duke](https://wiki.debian.org/DebianDuke) [namesake](https://disney.fandom.com/wiki/Duke_Caboom)|||||
|14|[Forky](https://wiki.debian.org/DebianForky) [namesake](https://disney.fandom.com/wiki/Forky)|||||
|13|[Trixie](https://wiki.debian.org/DebianTrixie) [namesake](https://disney.fandom.com/wiki/Trixie)|[2025-08-09](https://www.debian.org/News/2025/20250809)||||
|12|[Bookworm](https://wiki.debian.org/DebianBookworm) [namesake](https://disney.fandom.com/wiki/Bookworm)|[2023-06-10](https://www.debian.org/News/2023/20230610)|2026-06-10|2028-06-30|[2033-06-30](https://www.freexian.com/lts/extended/docs/debian-12-support/)|
|11|[Bullseye](https://wiki.debian.org/DebianBullseye) [namesake](https://disney.fandom.com/wiki/Bullseye)|[2021-08-14](https://lists.debian.org/debian-announce/2021/msg00003.html)|[2024-08-14](https://www.debian.org/News/2024/20240814)|2026-08-31|[2031-06-30](https://www.freexian.com/lts/extended/docs/debian-11-support/)|
|10|[Buster](https://wiki.debian.org/DebianBuster) [namesake](https://disney.fandom.com/wiki/Buster_\(Toy_Story\))|[2019-07-06](https://lists.debian.org/debian-announce/2019/msg00003.html)|[2022-09-10](https://www.debian.org/News/2022/20220910)|[2024-06-30](https://lists.debian.org/debian-lts-announce/2024/05/msg00002.html)|[2029-06-30](https://www.freexian.com/lts/extended/docs/debian-10-support/)|

```sh
# netinst images are "isohybrid" and can be written to usb via:
cp <file> <device>
sudo dd if=<file> of=<device> bs=16M status=progress oflag=sync
```

# apt without network
Two methods
## dpkg
- Not 100% package manager compliant if systems are not identical

```sh
#Download package and dependencies into current directory
apt download <package_name>

# move files and install with
dpkg -i *.deb
```

## 3rd party apt-offline
https://github.com/rickysarraf/apt-offline

1. On OFFLINE machine, generate the packages and signature for what to install
```sh
# UPDATE existing
sudo apt-offline set --update apt-offline.sig

# INSTALL new
sudo apt-offline set --install-packages <package_name> --update apt-offline.sig
```

2.  copy `apt-offline.sig` file to ONLINE machine

3. On ONLINE machine, use the signature to download the packages into `bundle.zip`
```sh
apt-offline get --bundle bundle.zip apt-offline.sig
```

4. Copy `bundle.zip` back to OFFLINE machine

5. On OFFLINE machine, apply the downloaded packages and updates
```sh
sudo apt-offline install bundle.zip
```

6. On OFFLINE machine, use `apt` normally to install
```sh
sudo apt install <package_name>
```
# C and C++
https://packages.debian.org/sid/build-essential
https://packages.debian.org/stable/devel/linux-libc-dev

build-essential is required for building debian packages. It combines `dpkg-dev` with  c/c++ tools
```sh
# or substitute "libc-dev" for "libc6-dev"
sudo apt install gcc g++ libc6-dev make # dpkg-dev
```

Other useful tools
```sh
sudo apt install binutils autotools
```

# packages c++ and nice explanation
https://www.reddit.com/r/linux4noobs/comments/17ooche/comment/k81qj3n/
```
[afiefh](https://www.reddit.com/user/afiefh/)

• [2y ago](https://www.reddit.com/r/linux4noobs/comments/17ooche/comment/k81qj3n/)

Understanding this involves a little side trip down package management lane.

Disclaimer: This is slightly simplified to make it easier to understand.

You're likely familiar with Windows-land, where you download an installer, it installs stuff on your system, end of story. Everything the program needs to run is packaged along with that installer. In Linux most distros rely on package managers, which contain a list of all the different dependencies a certain package may have. So for example a little program like [KCalk](https://apps.kde.org/kcalc/) requires something in order to handle the graphical user interface, in this case it would use [the Qt library](https://packages.debian.org/search?keywords=libqt). So when you install KCalk, the package manager checks whether Qt is installed, and if it's not it installs it as well.

Having this system in place allows for something called "meta packages". Basically a package that in itself contains nothing, but is only designed so that you can install all its dependencies in one go. Think of it like telling your buddy "Buy all the things required for superbowl night" rather than listing the items.

In the case of [Debian's Build Essential package](https://packages.debian.org/sid/build-essential) the dependencies are:

- dpkg-dev: The development headers for the Debian package manager. Probably not useful for your needs.
    
- g++: The GNU C++ compiler.
    
- gcc: The GNU Compiler Collection, in case you need to compile C.
    
- libc6-dev: The libc standard library.
    
- libc6.1-dev: Same as the above, different version.
    
- make: [GNU Make](https://www.gnu.org/software/make/), this is used to build larger packages of software i.e. if your program is 50 files, Make knows how to compile them and link them together, instead you manually calling `g++` 50 times.
    

You could in theory just install these packages manually, but trust me, learning C++ is one hell of a rabbit hole to jump into. Just take the build-essential package and move forward. Trying to fiddle with these things comes a long long time after the "starting C++" point of your journey.

Another piece of advice regarding C++: The language was first standardized in 1998, and it existed in various non-standard forms before that. This means the language is more than 25 years old at this point, and it was based on C which was standardized in 1978. There is a lot of old stuff in the language. When you find something weird in the language and ask yourself "why is this so fucked?" the answer is usually because of "historical baggage and backwards compatibility." Try to learn "modern C++" i.e. at the very least the 2011 version known as C++11 as there were huge changes to the language in that version. C++17 and C++23 are also available, and while the changes are amazing, they are a smaller jump than C++11 was.

[](https://www.reddit.com/user/peepeepoopoo_gang/)

[](https://www.reddit.com/user/peepeepoopoo_gang/)[](https://www.reddit.com/user/peepeepoopoo_gang/)
```