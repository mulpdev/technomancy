Python 2.7.18 was last in default repo of
- Ubuntu 22.04 Jammy Jellyfish
- Debian 11 bullseye

# Build from source
Github Tag is v2.7.18
https://github.com/python/cpython/releases/tag/v2.7.18
https://github.com/python/cpython/tree/8d21aa21f2cbc6d50aab3f420bb23be1d081dac4

```sh
#sudo apt install build-essential
sudo apt install autotools-dev make 
tar xvf cpython-2.7.18.tar.gz
cd cpython-2.7.18/
```

```sh
#--enable-optimizations 
#--disable-shared LDFLAGS="-static"
# optimize & static options CAN be combined
./configure 
make
#sudo make install # /usr/bin/
./python
```

# Snippets

## String Formatting Hex

static width
```python
'''
{ - begin format
: - spacing ...
0 - ... prepend character ...
8 - ... n times ...
X - uppercase hex chars, use x for lowercase (printf)
} - end format
'''
>>> "0x{:08X}".format(42)
>>> '0x0000002A'
```

variable width
```python
'''
{ - begin format
0 - index of parameter to format
# - use the "0x" prefix
0 - fill with zeroes ...
{1} - ... to length of parameter at this index (including 0x)
x - lowercase hexadecimal, for uppercase use X and append .replace('X', 'x') because ALL alphabet characters are now uppercase, including 0X

} - end format
'''
>>> "{0:#0{1}x}".format(42,6)
'0x002a'
```
