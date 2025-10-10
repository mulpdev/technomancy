# C isms
`x[y] == y[x] == *(x+y) == *(y+x)`
- As long as one side is a pointer expression and the other is an integral
	- int is intrinsic
	- == is mathematically commutative operator
- The array subscript/index `ptr[idx]` is equivalent to the pseudo code

```c
base_address = &ptr // ex 0x20000

/* base type of pointer has a specific width bytes
so the offset (in bytes) is the idx multiplied by width */*
offset_in_bytes = base_type(ptr) * idx 

final_addr = base_address + offset_in_bytes
```


```c
int x[5] = { 2,4,6,8,10 };
int y = 3;
int a = x[2]; // a = 4

/*
English: "Assign the third (b/c zero based) integer, starting at address x, to the variable a"

x is a pointer to an integer at in arbitrary address
	- the fact that it is an array makes no difference

pointer_base := [&x] // deref address of x to get first integer being pointed to

offset := sizeof(int) * 2 // width of int * idx
*/
```

# inline ASM gcc

```c
int add(int i, int j)
{
	int res = 0;

	asm( /* or asm volatile if processor side effects */
		"ADD R0, %[input_i], %[input_j]\n\t" /* multiline MUST end in \n\t */
		: [result] "=r" (res) /* output operand list, omit if empty */
		: [input_i] "r" (i), [input_j] "r" (j) /* input operand list, omit if empty */
		: /* clobbered regs, lowercase, omit if empty. DO NOT SAVE REG MANUALL */
	);

	return res;
}
```

An `operand`, ex `[input_i] "=r" (res)` consists of three things
	1. A symbolic name in `[]`
	2. A constraint string
	3. A C expression in `()`