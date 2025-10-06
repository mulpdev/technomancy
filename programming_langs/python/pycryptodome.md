```bash
pip3 install pycryptodome
```

```python
from Crypto.Cipher import AES
import binascii

# 0x100003f00 arm64
# direct copy of bytes in address order
xf00 = '3e91ad3026a51162'
xf08 = 'ca9c57402a004581'
xf10 = '42627c0905b385f7'
xf18 = '02a1c55d067f7a77'
xf20 = 'e0'*16

data_hexstr = xf00 + xf08 + xf10 + xf18
key_hexstr = xf20

data = binascii.unhexlify(data_hexstr)
key = binascii.unhexlify(key_hexstr)
print('DATA:\t' + data_hexstr)
print(b'DATA:  ' + data)
print('KEY :\t' + key_hexstr)
print(b'KEY :  ' + key)

dec = AES.new(key, AES.MODE_ECB)
#dec = AES.new(key, AES.MODE_ECB, iv) # for non ECB modes
cipher = dec.decrypt(data)
print(b'CIPHER:  ' + cipher)

enc = AES.new(key, AES.MODE_ECB)
#enc = AES.new(key, AES.MODE_ECB, iv) # for non ECB modes
plain = enc.encrypt(data)
print(b'PLAIN :  ' + plain)
```