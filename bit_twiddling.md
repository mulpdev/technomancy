# Specific Bit Constant Chart

OR these together for specific bits

Constants for each bit
- group into nibbles/hex digits
- bits 1-4 correspond to hex(1,2,4,8)

```python
# bit: pow(2, (n-1))
01: 0x1  05: 0x10  09: 0x100  13: 0x1000
02: 0x2  06: 0x20  10: 0x200  14: 0x2000
03: 0x4  07: 0x40  11: 0x400  15: 0x4000
04: 0x8  08: 0x80  12: 0x800  16: 0x8000
```

```python
# Arbitrary int x
for i in range(len(bin(x)[2:])): print(i+1) if ((x >> i) & 1 == 1) else None
```

# Masks

| Operation        | Logical              |
| :--------------- | :------------------- |
| Set bit          | OR 1 (OR mask)       |
| Clear bit        | AND 0 (AND NOT mask) |
| Query bit        | AND 1                |
| Toggle bit       | XOR 1                |
| Multiply by 2^^n | Left Shift n         |
| Divide by 2^^2   | Right Shift n        |



# Shifts
`>>` n
- drops lowest n bits
- moves bit n+1 to lowest bit
- 0x1f = 31

# Bit Stuff
`>>` n
- drops lowest n bits
- moves bit n+1 to lowest bit
- 0x7 = 8, 0xf = 15, 0x1f = 31, 0x2f = 63

Set bit - OR 1 (OR mask)
Clear bit - AND 0 (AND NOT mask)
Query bit = AND 1 (AND mask)
Toggle bit = XOR 1
Mult 2^n - `<< n`
Div 2^n - `>> n`

Creating a mask
- Group into nibbles/hex digits
- bits 1-4 correspond to hex(1,2,4,8)
- Sum the values for each bit you want to check 

LSB index to values
```
01: 0x1    05: 0x10   09: 0x100  13: 0x1000 
02: 0x2    06: 0x20   10: 0x200  14: 0x2000 
03: 0x4    07: 0x40   11: 0x400  15: 0x4000 
04: 0x8    08: 0x80   12: 0x800  16: 0x8000

Ex: to check bits 1,4,12
0x1 + 0x8 + 0x800 = 0x809
```

Determine which bits are set in an integer
```python
# Convert to binary string, test bit 1, shift next bit into 1, loop
x = 42
for i in range(len(bin(x)[2:])): print(i+1) if ((x >> i) & 1 == 1)
```


# LSFR

1. Registers are numbered 1-n from left to right
2. Taps are set on some of the registers (preferably with no common denominator)
3. Calculate polynomial using the following formula
	- `poly = x^^(tap reg 1) + x^^(tap reg 2) ... + 1`

Example: 28 bits, with taps on bits 7, 13, 22
- I think LSB vs MSB matters

```c
/*
MSB
                  t1                t2                         t3
01 02 03 04 05 06 07 08 09 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28

LSB
                  t1                         t2                t3
28 27 26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01

poly = x^22 + x^13 + x^7 + 1
*/

state = something

# >> (max bits - tap bit - 1) for each tap
bit = state ^ (state >> 5) ^ (state >> 14) ^ (state >> 20) & 1
```

ex2 27 bits with taps in bits 9, 13, 22
```
						t1          t2                         t3
01 02 03 04 05 06 07 08 09 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27

state = number created from max bits at start
bit = state ^ (state >> (27-9)) ^ (state >> (27-13)) ^ (state >> (27-22)) &1 
```