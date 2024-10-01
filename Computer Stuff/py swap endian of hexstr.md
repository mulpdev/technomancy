```python
import binascii

def swap_endian(s1):
    return "".join(map(str.__add__, s1[-2::-2] ,s1[-1::-2]))

le_hexstr = 'aabbcc010203'
le_binary = binascii.unhexlify(le_hexstr)

be_hexstr = swap_endian(le_hexstr)
be_binary = binascii.unhexlify(be_hexstr)
```