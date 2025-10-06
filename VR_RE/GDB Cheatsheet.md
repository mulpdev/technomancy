`.gdbinit` path locations
- local directory for current run
- `$HOME` for 'global' settings
# Args
```
(gdb) run params ... < in.txt

(gdb) r < <(python3 -c 'print("A"*0x54 + "B"*0x8)'; echo "­inp­ut2­")

$ gdb --args ./binary `python -c 'import struct; print ";sh;" + "A" * 503 + struct.pack("Q", 0x7ffff7a3b3d7)'`
```
## Display
`p` (print) or `inspect` is for data
- `print [options] /f <expression>`
	- `options` https://sourceware.org/gdb/current/onlinedocs/gdb.html/Data.html#Data
	- `f` format
	- `<expression>` what to print (in source lang)

`x` (eXamine) or is for memory
- `x/nfu <address>`
	- `n` OPTIONAL is count, can be negative or positive 
	- `f` format
	- `u` unit size
	- `<address>` is the starting point for count
### format (`x` and `p`/`inspect`)

| x   | hex                                        |
| :-- | :----------------------------------------- |
| d   | decimal                                    |
| u   | unsigned decimal                           |
| o   | octal                                      |
| t   | binary (`t` for "two")                     |
| a   | address (hex + offset from nearest symbol) |
| c   | char (ASCII or `\nnn` for 128+)            |
| f   | float                                      |
| s   | c-string                                   |
| r   | raw? uses a python pretty-printer?         |
| z   | like `x` with padded 0's to sizeof(int)    |
| i   | instructions                               |
| m   | memory tags                                |
### unit size

| Bytes | GDB | GDB mnemonic |     | Windbg | Windbg mnemonic |
| ----- | --- | ------------ | --- | ------ | --------------- |
| 1     | b   | byte         |     | b      | byte            |
| 2     | h   | half-word    |     | w      | word            |
| 4     | w   | word         |     | d      | double world    |
| 8     | g   | giant        |     | q      | quad word       |

## printing an array
`print/<format> *<symbol|var|addr>@<cnt>`

`char ** line environ` https://stackoverflow.com/questions/32917033/how-to-get-environment-of-a-program-while-debugging-it-in-gdb/76903706#76903706
in `.gdbinit`:
```bash
define print_environ
    set $i = 0
    while (environ[$i] != 0x0)
        printf "environ[%i]: \e[;94m%p\e[m: \"%s\"\n", $i, environ[$i], environ[$i++]
    end
end
```
# Program manipulation
`continue n` where n is the nuber of times to continue

Registers don't need a type, memory assignment DOES
ex: 
- `set $rip = main` or  `set $rip = $eax` or `set $rip = 0x4141414152525252`
- `set {char[7]}0x555555556004 = “AAAA%d”` 

Can set a source code variable directly
`set var i=2`

# Type Information
### Make a type without debug symbols
- See: https://stackoverflow.com/questions/7272558/can-we-define-a-new-data-type-in-a-gdb-session
- Create simple C source file with structure definitions
- compile only, with debug symbols
	- `gcc -g -c sample.c`
- Add to gdb session using `add-symbol-file` and supplying type to `p`
```
(gdb) add-symbol-file sample.o 0
add symbol table from file "sample.o" at
    .text_addr = 0x0

(gdb) p (struct sample *)0x7fffffffd6b0
$1 = (struct sample *) 0x7fffffffd6b0
(gdb) p *$1
$2 = {i = 42, less = 0x7fffffffd6b0, more = 0x7fffffffd6b0}
```

### Displaying Type info
`complex datatype` means `class typedefs of structs, classes and unions`

`info types [regex]` prints all types in program that match the optional regex
- `info types uint*`

`whatis [/flags] <arg>`  
- if `<arg>` is variable/expression it prints literal source code type
	- Does NOT follow typedef or print fields of complex data types
- if `<arg>` is type name it unrolls ONE LEVEL of typedef

`ptype [/flags] <arg>` 
- always unrolls typedefs unless inside a complex datatype
	- unroll those by specifying the field as the `<arg>`

`explore <arg>` 
- <mark style="background: #FFF3A3A6;">NOTE: ONLY if gdb build configured with `--with-python`</mark>
- very verbose typing info following typedefs
```
(gdb) explore cs
The value of `cs' is a struct/class of type `struct ComplexStruct' with
the following fields:

  ss_p = <Enter 0 to explore this field of type `struct SimpleStruct *'>
   arr = <Enter 1 to explore this field of type `int [10]'>

Enter the field number of choice:
```

```
(gdb) explore struct ComplexStruct
```

```
(gdb) explore value
The value of 'value' is of type 'uint32_t' which is a typedef of type 'unsigned int'
'value' is a scalar value of type 'unsigned int'.
value = 1234
```
# PEDA/pwndebg specific
`context` reprints the default output when gdb stops program

`skipi <cnt>` Advance IP but DO NOT execute instructions

`hexdump <address> /<cnt>`

The output of the context may be redirected to a file (including other tty) by using `set context-output /path/to/file` while leaving other output in place.

