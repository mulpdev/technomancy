# tl;dr
Defined by ABIs for usermode and the OS for syscalls

Makes uses of a software interrupt or a dedicated assembly instruction

First stop for 90% case is:
- https://wiki.osdev.org/Calling_Conventions
- https://en.wikibooks.org/wiki/X86_Assembly/Interfacing_with_Linux#Via_interrupt

https://elixir.bootlin.com/linux/latest/source

# User Mode
## Cleanup Conventions

In x86/x86_64 
- caller clean `ret`
- callee clean `ret X` 
	- X is numbers of bytes NOT INCLUDING RETURN PTR

Variable args on stack MUST be caller clean


| Convention  | Cleanup                    | Notes                                                                                            |
| ----------- | -------------------------- | ------------------------------------------------------------------------------------------------ |
| cdecl       | caller `ret`               | C standard (and C++)                                                                             |
| stdcall     | callee `ret X`             | Win32 API                                                                                        |
| fastcall    | callee                     | ECX, EDX for param1 and param2, <br>Windows x32                                                  |
| thiscall    | caller(GCC)<br>callee(WIN) | C++ objects with inheritance<br><br>`this` pointer in ECX<br><br>name mangling unless `extern c` |
|             |                            |                                                                                                  |
| Vector Call | callee                     | graphics/game design in 2013                                                                     |


## ABI References

System V ABI (not-ELF) https://gitlab.com/x86-psABIs
- ex. See A.2.1 p 147 in [x86_64 ABI](https://raw.githubusercontent.com/wiki/hjl-tools/x86-psABI/x86-64-psABI-1.0.pdf)

Public folder for ELF ABI specs: https://refspecs.linuxfoundation.org/elf/

| Platform               | Return   | Params                                   | Stack Alignment | Preserved                                   | Ref                                                                                                                                                                                                    |
| :--------------------- | :------- | :--------------------------------------- | :-------------- | :------------------------------------------ | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| System V i386          | eax, edx | stack R to L                             | 4 byte          | ebx, esi, edi, ebp, esp                     | 3.2 [ELF x86_64 ABI](https://refspecs.linuxfoundation.org/elf/x86_64-abi-0.99.pdf)                                                                                                                     |
| System V X86_64        | rax, rdx | rdi, rsi, rdx, rcx, r8, r9. stack R to L | 16 byte at call | rbx, rsp, rbp, r12, r13, r14, r15           | 3.9 [ELF ABI](https://refspecs.linuxfoundation.org/elf/abi386-4.pdf)                                                                                                                                   |
| System V ARM32         | r0, r1   | r0, r1, r2, r3<br>stack                  | 8 byte          | r4 - r11, r13, r14                          | [aapcs32](https://github.com/ARM-software/abi-aa/blob/2982a9f3b512a5bfdc9e3fea5d3b298f9165c36b/aapcs32/aapcs32.rst)<br>[ARM](https://developer.arm.com/Architectures/Application%20Binary%20Interface) |
| System V AARCH64       | x0, x1   | x0 - x7,<br>stack                        | 16 byte         | x19 - x29                                   | [aapcs64](https://github.com/ARM-software/abi-aa/blob/2982a9f3b512a5bfdc9e3fea5d3b298f9165c36b/aapcs64/aapcs64.rst)<br>[ARM](https://developer.arm.com/Architectures/Application%20Binary%20Interface) |
| System V PowerPC 32bit | r3, r4   | r3 - r10,<br>stack                       | 8 byte?         | r1, r2 (system) r14 - r31                   | 3.14 [ELF PPC](https://refspecs.linuxfoundation.org/elf/elfspec_ppc.pdf)                                                                                                                               |
|                        |          |                                          |                 |                                             |                                                                                                                                                                                                        |
| Microsoft x86          | eax, rdx | stack R to L                             | 4 byte          | esi, edi, ebx, ebp                          | [MSDN](https://learn.microsoft.com/en-us/cpp/cpp/argument-passing-and-naming-conventions?view=msvc-170)                                                                                                |
| Microsoft x86_64       | rax, rdx | rcx, rdx, r8, r9, <br>stack R to L       | 16-byte at call | rbx, rdi, rsi, rsp, rbp, r12, r13, r14, r15 | [MSDN](https://learn.microsoft.com/en-us/cpp/build/x64-calling-convention?view=msvc-170)                                                                                                               |

## Prologue / Epiologue x86/x86_64
- Store base pointer
- move stack pointer to base pointer (starts new stack frame)
- save off clobbered registered that are preserved
- make room on stack for local variables (stack grows high-to-low)
```
push Xbp
mov Xbp, Xsp
<push saved registers>
sub Xsp, BYTES

...

add Xsp, BYTES
<pop saved registers>
mov Xsp, Xbp
pop Xbp
```

# Kernel Mode / Syscalls

Usermode program invokes a trap via interrupt or specific instruction

## Cleanup Conventions
Clean up is handled by kernel. Syscalls have their own stack not accessible to user mode program that invoked the system call

## Syscall calling conventions reference
For Linux: `man 2 syscall`

| Platform            | Return Value | Instruction | Syscall Number | Parameters                   | Scratch<br>Regs | Notes                                                                                                                                                                                                                                                                                                               |
| ------------------- | ------------ | ----------- | -------------- | ---------------------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Linux i386          | eax          | `int 0x80`  | eax            | ebx, ecx, edx, esi, edi, ebp | eax             | Uses the same stack frame as the x86-64 version.	<br><br>All registers except eax must be saved<br>[source](https://github.com/torvalds/linux/blob/v3.13/arch/x86/ia32/ia32entry.S#L378-L397)<br><br>[Linux Kernel - ia32entry.S](https://github.com/torvalds/linux/blob/v3.13/arch/x86/ia32/ia32entry.S#L378-L397) |
| Linux X86_64        | rax          | `syscall`   | rax            | rdi, rsi, rdx, r10, r8, r9   | rcx, r11        | [A.2.1 p147](https://raw.githubusercontent.com/wiki/hjl-tools/x86-psABI/x86-64-psABI-1.0.pdf)                                                                                                                                                                                                                       |
| Linux ARM32         | r0           | `swi 0x0`   | stack          | r0-r7                        | ?               |                                                                                                                                                                                                                                                                                                                     |
| Linux AARCH64       | x0           | `svc 0`     | x8             | x0-x5                        | ?               |                                                                                                                                                                                                                                                                                                                     |
| Linux PowerPC 32bit | r3, r0 error | `sc`        | r0             | r3-r10, stack                | r1 (SP)         | Apple PPC slightly different                                                                                                                                                                                                                                                                                        |
| Microsoft x32       | ?            | `int 0x80`  | eax            |                              |                 |                                                                                                                                                                                                                                                                                                                     |
| Microsoft x64       |              |             |                |                              |                 |                                                                                                                                                                                                                                                                                                                     |

## Syscall numbers reference

| Arch                 | Link                                                                                                                            |
| :------------------- | :------------------------------------------------------------------------------------------------------------------------------ |
| Linux i386 `int80`   | [2.0.40/arch/i386/kernel/entry.S](https://elixir.bootlin.com/linux/2.0.40/source/arch/i386/kernel/entry.S#L543)                 |
| Linux i386 `syscall` | [arch/x86/entry/syscalls/syscall_32.tbl](https://github.com/torvalds/linux/blob/master/arch/x86/entry/syscalls/syscall_32.tbl)  |
| Linux x86_64         | [arch/x86/entry/syscalls/syscall_64.tbl](https://github.com/torvalds/linux/blob/master/arch/x86/entry/syscalls/syscall_64.tbl ) |
|                      |                                                                                                                                 |
| Linux ARM            | [arch/arm/tools/syscall.tbl](https://elixir.bootlin.com/linux/latest/source/arch/arm/tools/syscall.tbl)                         |
| Linux AARCH64        | [include/uapi/asm-generic/unistd.h](https://elixir.bootlin.com/linux/latest/source/include/uapi/asm-generic/unistd.h)           |
|                      |                                                                                                                                 |
| Windows Generic      | Diassemble ntdll.dll                                                                                                            |
| Windows x86          | https://j00ru.vexillium.org/syscalls/nt/32/                                                                                     |
| Windows x86_64       | https://j00ru.vexillium.org/syscalls/nt/64/                                                                                     |

Other Windows System calls/kernel/assembly links
- https://sonictk.github.io/asm_tutorial/#windows:thewindowtothehardware/themicrosoftx64callingconvention
- https://alice.climent-pommeret.red/posts/a-syscall-journey-in-the-windows-kernel/
