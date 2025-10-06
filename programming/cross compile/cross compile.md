# Terminology
Source: 
- https://gcc.gnu.org/onlinedocs/gccint/Configure-Terms.html
- https://www.linuxfromscratch.org/lfs/view/stable/partintro/toolchaintechnotes.html

`BUILD` - machine doing the compiling/linking
`HOST` - machine that will run the compiled/linked binary
`TARGET` - (Only for a compiler being built) machine the compiled/linked binary emits code/instructions for

if `BUILD == HOST == TARGET`
- "native"

if `BUILD == HOST, TARGET`
- "cross"

if `BUILD, HOST, TARGET`
- "Canadian" colloquial reasons related to Canada political party?
- Example: PowerPC machine makes compiler that runs on x86_64 that makes binaries for ARM

if `BUILD, HOST == TARGET`
- cross compiling a native for a different system
- aka "host-x-host", "crossed-native", "cross-built-native"

if `BUILD == TARGET, HOST`
- No name. It's using a cross compiler to build another cross compiler2 for `BUILD` arch
# Triplets
Specify TARGET arch of compiler or binutils. The standard identifier for GNU binutils but has also been adapted for others like MUSL

- arch - `x86_64 i686 arm mips sparc` 
- vendor - `pc apple none`
- OS - `linux windows bsd`
	- can also be ABI  like in  `arm-linux-eabi`
	- <font color="#FF1493">MUSL</font> appends "musl" in this field as an identifier

```sh
arm-linux-gnueabi-gcc file.c

CC=arm-linux-gnueabi-gcc make

arm-linux-gnueabi-objdump -D a.out

gdb-multiarch a.out

# if needed add: -L /ARM-elf-intepreter/lib/
qemu-aarch64 /path/to/aarch64_bin
```

# musl 
- https://github.com/richfelker/musl-cross-make
- alpine linux