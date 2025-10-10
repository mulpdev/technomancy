NOTE: 
- Go find the C++ structs and stuff in Ghidra and how they are applied
- Go document in GDB c++


# Ghidra-ism
This occurs because the structure editor showing relationships as if they were C structs
```c
/* This is a function call using a fptr callback
of a child class to a parent vftable at offset 0x124 */

iVar1 = (struct_t *)(**(code **)(int)(this_ChildClass1->vftable).fieldX_0xYY.fieldA_0xBB + 0x124))();
```

## Fix it



`Child` is a C++ Class that inherits from `Parent` class

A C++ class is represented as a struct in Ghidra. For `Child` it looks like

| Offset | Length | Mnemonic | DataType | Name |
| ------ | ------ | -------- | -------- | ---- |
| 0x0 | 0x4 | Child_vftable* | Child_vftable* | vftable |

`Child_vftable` is of size XX and because of inheritance, the first chunk of it is an embedded copy of the `Parent_vftable` of size YY, and the remaining bytes are pointers to the `Child` class functions

| Offset | Length | Mnemonic        | DataType        | Name    |
| ------ | ------ | --------------- | --------------- | ------- |
| 0x0    | XX     | Parent_vftable* | Parent_vftable* | vftable |

Finally set `Parent_vftable` + 0x124 to be the name of the function

| Offset | Length | Mnemonic | DataType | Name                      |
| ------ | ------ | -------- | -------- | ------------------------- |
| 0x124  | 0x4    | void*    | void*    | name::space::functionname |

# From mangled string to vtable

If you have a mangled string in the disassembly, here is how to get to to the vtable

```
XREF of string points to typeinfo + 0x4

Look at non-call (i.e., XREF 80001000(*) ) xrefs of typeinfo struct
typeinfo --xref--> vtable + 0x4

If next pointer is a function, you are in a vptr table
Else you are in the parent (typeinfo + 0x8) of another class
```


# diagrams

```
class obj
0x0 void* vptr
0x8 member vars
0xN void* parent_obj

vtable
0x0 undefined8 top_offset
0x8 typeinfo* typeinf_obj
0x10 void* v_fptr

typeinfo
0x0 void* typeinfo_vptr
0x8 char* type_name
0x10 void* Parent_typeinfo


```


# cppobj2
# Single Inheritence

## Source
```cpp
class A{
	public:
	int al
	virtual void purex() = 0;
	virtual void x() {};
};

class C: public A {
	public:
	int c;
	virtual void purex() {}
	virtual void x() {}
};

int main()
{
	C obj;
	cobj.c = 0xdeadbeef;
	return 0
}
```

## gdb

```cpp
(gdb) set disassembly-flavor intel
(gdb) start
```

Disassemble main
```cpp
(gdb) disass main

COPY PASTA
```

Set demangle and disassemble main again

```cpp
(gdb) set print asm-demangle on

(gdb) set print demangle on

(gdb) disass main

COPY PASTA
```


## Class Object
aka `C cobj;`

```cpp
C cobj; // from src. cobj is pointer at rbp-0x20

lea rax [rbp-0x20]
mov rdi, rax
call 0xYYYYYYYYYYY <C::C()> // Formerly <_ZN1CC2Ev


cob.c = 0xdeadbeef;
mov DWORD PTR[rbp-0x14], 0xdeadbeef
```

```cpp
(gdb) x/gx $rbp-0x20
COPY OUTPUT LINE STACK. 0x7ff: 0x555

(gdb) x/gx *(long*)($rbp-0x20)
0x5555 <vtable for C+16>: 0x5555
```

## object (cobj variable)


| Data Type | Description | Notes |
| --------- | ----------- | ----- | 
| Class *   | param_1 to constructor  | ptr to Class Vtable +0x10 |


| Data Type | Addr | Value | Notes |
| --------- | ----------- | ----- | --- |
| Class *   | 0x7fff  | 0x5555 | vtable for C + 0x10 |

```cpp
(gdb) x/20 *(long*)($rbp-0x20)-0x10
0x 5555 <vtable for C>
...
0x555 <typeinfo for C>
...
0x555 <typeinfo for A>
```

## vtable

| Data Type | Name                                        | Notes                                   | Requirements                                                            |
| --------- | ------------------------------------------- | --------------------------------------- | ----------------------------------------------------------------------- |
| int       | virtual-base offset                         | offset to Parent's data/methods         | Optional. Virtual inheritence, one copy of base class in concrete class |
| int       | top_offset                                  | offset to access Child overriden method | Optional? Child class must override a Parent's function                 |
| typeinfo* | typeinfo                                    | pointer to typeinfo                     | Required by Itanium ABI (clang, gcc)                                    |
| fptr      | Left-most Parent (in src) function pointers | Constructor, Destructor, etc            | Optional?. I guess you can make a data only class                       |
| fptr      | child function pointers                     | Constructor, Destructor, etc            | Optional?. I guess you can make a data only class                       |

If there are multiple Parent classes, the next Parent (left-most + n) is appended immediately after

Class C

| Data Type           | Addr  | Value | Notes      |
| ------------------- | ----- | ----- | ---------- |
| virtual-base offset | n/a   |       |            |
| top_offset          | 0x555 | 0x0   |            |
| Class C typeinfo*   | 0x555 | 0x555 |            |
| fptr                | 0x555 | 0x555 | C::purex() |
| fptr                | 0x555 | 0x555 | C::x()     |
Class A

| Data Type           | Addr  | Value | Notes                               |
| ------------------- | ----- | ----- | ----------------------------------- |
| virtual-base offset | n/a   |       |                                     |
| top_offset          | 0x555 | 0x0   |                                     |
| Class C typeinfo*   | 0x555 | 0x555 |                                     |
| fptr                | 0x555 | 0x555 | `__cxa_pure_virtual` aka A::purex() |
| fptr                | 0x555 | 0x555 | A::x()                              |

## typeinfo

```cpp
(gdb) x/10x 0x555
0x555 <typeinfo>
...
```


| Data Type      | Name                                | Notes                         | Requirements                        |
| -------------- | ----------------------------------- | ----------------------------- | ----------------------------------- |
| vtable+0x16 ** | Functions of `__si_class_type_info` | Helper functions for typeinfo |                                     |
| string/char*   | Mangled C++ Name                    |                               |                                     |
| typeinfo*      | Parent typeinfo (or 0)              |                               |                                     |


