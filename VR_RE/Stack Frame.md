# x86
- Stack grows high to low
- Return address is EBP + 4
- First stack var is EBP - 4
```
 # Create Stack Frame
 8048485:       55                      push   %ebp
 8048486:       89 e5                   mov    %esp,%ebp
 
 # Save clobbered saved register (if needed)
 8048488:       51                      push   %ecx

 # Increase stack size
 8048489:       83 ec 54                sub    $0x54,%esp

```

# x86_64
- Stack grows high to low
- Return address is RBP + 8
- First stack var is RBP - 8
```
0x00000000004004cc <+0>:     push   rbp
0x00000000004004cd <+1>:     mov    rbp,rsp
0x00000000004004d0 <+4>:     sub    rsp,0x98
```

# PowerPC
At entry, `r1` is still previous functions stack pointer
- This value of `r1 + 4` will be LR for this function

Prologue contains the following


```
# Make stack by subtracting pointer
stwu r1, 0xABC

# Move LR onto stack via r0
# note it's 4 more bytes than the stack size subtracted above
mfspr r0,LR
...SNIP...
stw r0,0xAC0(r1)
```

# ARM
Push and stmXY instructions manipulate SP

Ghidra will show stack_X where X will be larger than the constant in the prologue's  sub sp, Y instruction

Stack_10
Stack_14
Stack_18

Copy in "tic TAC toe"

Stack_10= "toe0"
Stack_14= "TAC "
Stack_18= "tic "