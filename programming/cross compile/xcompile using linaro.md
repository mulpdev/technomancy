sudo apt install zlib1g zlib1g:i386

export XTOOLS_PATH=/home/user/xcomp/gcc-linaro-arm-linux-gnueabihf-4.8-2014.03_linux/bin
export PATH=$XTOOLS_PATH:$PATH 

# zlib
cd zlib
CC=arm-linux-gnueabihf-gcc RANLIB=arm-linux-gnueabihf-ranlib LD=arm-linux-gnueabihf-ld AR=arm-linux-gnueabihf-ar CFLAGS="-g -DDEBUG" ./configure
make

# openssl
cd openssl/
git checkout OpenSSL_1_0_1c
CC="arm-linux-gnueabihf-gcc -g" RANLIB=arm-linux-gnueabihf-ranlib LD=arm-linux-gnueabihf-ld AR=arm-linux-gnueabihf-ar ./Configure -DDEBUG -DL_ENDIAN -Lhome/user/code/zlib/ no-threads shared linux-generic32
make

# Ghidra
Import openssl/libcrypto.so.1.0.0 and openssl/libssl.so.1.0.0 (you CAN use .a files but you will have to search every .o file contained inside)
Analyze both with defaults + Decompiler Parameter ID + ELF Scalar Operand References, and Save the DB

# Enable Function ID
File > Configure > Developer > check 
Close code browser and reopen it
You should now see "Debug Search Window" and "Table Viewer" at the bottom of Tools > Function ID

# Create FIDB for program
Tools > Function ID > Create new empty FidDB
Tools > Function ID > Populate FidDB from programs

Fill out the form. 

Select the Ghidra DB folder containing both of the openssl binaries

For Language DO NOT TYPE, use the ... button and select
ARM	v8	32	little	default

Leave the common symbols file blank

Your db file will grow from ~670 bytes to ~415kb

# Import FidDB
Tools > Function ID > Attach existing FidDB file and give it the path
Analysis > One Shot > Function ID

NOTE1: Memory labels (DAT_) and parameter names will need to be copied over

NOTE2: Statically compiled libraries throw off the Function ID heuristics with different locations and slightly different branching instructions. 

Use strings and constants in the target binary, find a matching function with similar decompile (same strings/constants/flow control/etc), right click on the function > Edit Function Signature and copy the top most window

In the target binary browser, right click on the function > Edit Function Signature and paste into the top most window, accept the data types if needed.