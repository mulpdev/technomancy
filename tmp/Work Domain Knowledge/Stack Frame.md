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

