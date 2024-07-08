BROKEN!




Modified from: 

https://forums.freebsd.org/threads/howto-compile-toolchain-to-cross-compile-android-kernel.47766/

https://preshing.com/20141119/how-to-build-a-gcc-cross-compiler/


# Requirements

```
# Required
sudo apt install gawk bison flex texinfo automake libtool-bin libmpc-dev gperf 

# Specific cross-compiler triplet
sudo apt install gcc-arm-linux-gnuabi(hf)

# maybe? not sure exactly which platforms supported
sudo apt install gcc-multilib 
```

Kernel source
https://cdn.kernel.org/pub/linux/kernel/

Ghidra Project > Right click binary > About Binary
`ELF Note[required kernel ABI]:	Linux X.Y.Z`

# Steps
Make base dir (`$XCOMP_DIR`)
```
export XCOMP_DIR=/home/user/cross-compile

mkdir $XCOMP_DIR
cd $XCOMP_DIR
```

Extract: kernel-headers gcc-binutils gcc-linaro tarballs
```
tar xvf <blah>

$ ls -l
total 88
drwxrwxr-x 18 user user  4096 Mar 18 10:46 binutils-linaro-X.Y-YEAR.MO
drwxr-xr-x 35 user user  4096 Mar 18 10:55 gcc-linaro-X.Y-YEAR.MON
drwxr-xr-x 21 user user  4096 Jul 13  2008 linux-2.6.26
-rw-rw-r--  1 user user    69 Mar 15 16:55 hw.c
drwxrwxr-x  2 user user  4096 Mar 18 10:45 tarballs
```

Prepare environment
```
mkdir tools

export TOOLS_PATH=$XCOMP_DIR/tools
export SYSROOT_PATH=$COMP_DIR/linux-X.Y.Z
export TRIPLET=arm-linux-gnueabi
```

Binutils
use target `arm-elf` instead of `$TRIPLET` to for build of c runtime 
- https://stackoverflow.com/questions/3346173/why-is-a-gcc-cross-compile-not-building-crti-o
-
```
cd bintools-XYZ
mkdir build
cd build 

PATH=$TOOLS_PATH/bin:$PATH ../configure --target=$TRIPLET --prefix=$TOOLS_PATH --with-sysroot=$SYSROOT_PATH 

PATH=$TOOLS_PATH/bin:$PATH make
PATH=$TOOLS_PATH/bin:$PATH make install
```

gcc
```
cd bintools-XYZ
mkdir build
cd build 

PATH=$TOOLS_PATH/bin:$PATH ../configure --target=$TRIPLET --prefix=$TOOLS_PATH --with-sysroot=$SYSROOT_PATH --enable-languages=c

PATH=$TOOLS_PATH/bin:$PATH make
PATH=$TOOLS_PATH/bin:$PATH make install
```

## Troubleshooting / Alternative flags
- pthread.h: No such file or directory
	- Append`--disable-threads` to configure
