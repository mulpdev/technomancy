
**Hacky Table of Contents**

```query
file:registers_and_addressing /^#+ /
```

# [[#^b9139e]]

# Documentation Conventions

^b9139e

Documentation Conventions
- `Rn`  The register with the <font color="dodgerblue">base</font> address for calculation
- `Rm`  The register with the <font color="violet">offset</font> to add to `Rn`
- `Rd` or `Rt`  <font color="green">target (value to/from)</font> to do load or store 
- `!` This register is being modified (side effect?) by this instruction
# Registers
32-bit registers
`r0-r15`
- `r0-r10` No special/conventional use inside a function
	- `r0` Return Value
- `r11` aka `fp` Frame Pointer
- `r12` aka `ip`  Intra Procedure Call
- `r13` aka `sp` Stack Pointer
- `r14` aka `lr` Link Register
- `r15` aka `cpsr` Current Program Status Register

# Address Modes
## Offset Address Mode

ld/str <font color="green">r2 </font> [ <font color="dodgerblue">r1</font>, <font color="violet">r2</font> ]

Take address <font color="dodgerblue">Rm (base)</font> and add <font color="violet">Rn (offset)</font> to get operation address. Then do the `ld/str` operation with <font color="green">Rt (value to/from)</font>. Do not modify base register <font color="dodgerblue">r1</font>.
```c
// example store
uint32_t value = r2;
char*    base = r1;
uint32_t offset = r2; // second r2, inside [ ]

char* dest = (char*)(Rb + Rc)
(uint32_t *) dest = Rt;
```

## Pre-Indexed Address Mode

str <font color="green">r2 </font> [ <font color="dodgerblue">r1</font>, <font color="violet">r2</font> ]!

Take address <font color="dodgerblue">Rm (base)</font> and add <font color="violet">Rn (offset)</font> to get operation address. Then do the `ld/str` operation with <font color="green">Rt (value to/from)</font>. Finally modify base register <font color="dodgerblue">r1</font> to be <font color="dodgerblue">r1</font> + <font color="violet">r2</font>

```c
// example store
uint32_t value = r2; // first one, outside of [ ]
char*    base = r1;
uint32_t offset = r2; // second r2, inside [ ]

char* dest = (char*)(r1 + r2)
(uint32_t *) dest = value;
r2 += offset // the ! 
```

## Post-Indexed Address Mode
ld/str <font color="green">r3 </font> [ <font color="dodgerblue">r1</font> ],  <font color="violet">r2</font>

Take address <font color="dodgerblue">Rm (base)</font> to get operation address. Then do the `ld/str` operation with <font color="green">Rt (value to/from)</font>. Finally modify <font color="dodgerblue">r1</font> to be <font color="dodgerblue">r1</font> + <font color="violet">r2</font>

NOTE: <font color="green">Rd</font>  CANNOT BE <font color="dodgerblue">Rb</font>

It may be helpful to consider how some example C expressions map to these addressing modes:
```cpp
int a;       // r0
int *b;      // r1

a = b[1];    // ldr r0, [r1, #4]   (offset)
a = *(b+1);  // similarly

a = *(++b);  // ldr r0, [r1, #4]!  (pre-indexed)

a = *(b++);  // ldr r0, [r1], #4   (post-indexed)
```

# Instructions
Notes about instructions
## bcc / bcs
`BCS` / `BCC` = Branch Carry Set (C=1), Branch Carry Clear (C=0)

The instructions `BCS` / `BCC` can be used with `CMP` because `CMP` is a subtraction under the hood and ARM docs state "A carry occurs when the result of a subtraction is positive or zero"
## cmp
- From Azeria pg 253 Ch 7 Conditional Execution
- confirmed by ARM docs

`CMP` is a `SUBS` under the hood, where the S suffix sets the <font color="#FF1493"> NZCV</font> flags
- <font color="#FF1493"> N</font>egative
- <font color="#FF1493"> Z</font>ero
- <font color="#FF1493"> C</font>arry (aka Unsigned Overflow)
- o<font color="#FF1493">V</font>erflow

## Load/Store Multiple  
https://stackoverflow.com/questions/23045702/can-someone-help-me-understand-stmdb-ldmia-and-how-i-can-go-about-implementing  
  
https://azeria-labs.com/load-and-store-multiple-part-5/  
  
LDMxx - Load Multiple (from memory to registers)  
STMxx - Store Multiple (from registers into memory)  
  
Suffixes:  
- IA - Increment After  
- IB - Increment Before  
- DA - Decrement Before  
- DB - Decrement Before  
  
The increment/decrement is size of word (aka `sizeof(void*)`) so 4 or 8 bytes.  
  
In practice `ldm` == `ldmia` and `stm` == `stmia` are functionally equivalent.  
  
```armasm  
// words is an array of 32bit integers  
  
adr r0, words+12     /* address of words[3] -> r0 */  
  
// Start load exact    
ldmia r0, {r4-r6}    /* words[3] -> r4 = 0x03,  
                                               words[4] -> r5 = 0x04;    
                                               words[5] -> r6 = 0x05; */  
  
ldmib r0, {r4-r6}    /* words[4] -> r4 = 0x04;  
                                               words[5] -> r5 = 0x05;  
                                               words[6] -> r6 = 0x06 */  
  
// Modified from https://azeria-labs.com/load-and-store-multiple-part-5/  
```  
  
```armasm  
ldr r1, array_buff_bridge /* address of array_buff[0] -> r1 */  
  
stmia r1, {r4-r6}    /* r4 -> array_buff[0] = 0x03;    
                                               r5 -> array_buff[1] = 0x04;    
                                               r6 -> array_buff[2] = 0x05 */  
  
stmib r1, {r4-r6}    /* r4 -> array_buff[1] = 0x04;    
                                               r5 -> array_buff[2] = 0x05;    
                                               r6 -> array_buff[3] = 0x06 */  
```

