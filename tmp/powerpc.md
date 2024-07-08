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

