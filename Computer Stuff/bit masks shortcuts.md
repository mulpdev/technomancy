
| Operation        | Logical              |
| :--------------- | :------------------- |
| Set bit          | OR 1 (OR mask)       |
| Clear bit        | AND 0 (AND NOT mask) |
| Query bit        | AND 1                |
| Toggle bit       | XOR 1                |
| Multiply by 2^^n | Left Shift n         |
| Divide by 2^^2   | Right Shift n        |

# LFSR

registers numbered 1 - n, from left to right
taps set on some set of registers (preferable with no common denominator)
- taps = XOR locations

Polynomial = X^^(tap reg 1) + x^^(tap reg2) ... X^^(tap reg x) + 1
- +1 == x^^0 
Code = state ^ (state ^ (tap reg1) -1 ) ^ ((tap reg2) -1 )  ... ^ ((tap regn) -1 ) & 1 

ex 27 bits with taps in bits 9, 13, 22
```
						t1          t2                         t3
01 02 03 04 05 06 07 08 09 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27

state = number created from max bits at start
bit = state ^ (state >> (27-9)) ^ (state >> (27-13)) ^ (state >> (27-22)) &1 
```