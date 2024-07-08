Use constants defined in limits.h to avoid overflows and underflows

# Fuzzing
mutation of known good input
- might have issues with checksums and obfuscation layers

Ascii protocol
- extend chars
- remove chars
- extend "control" chars
	- `</html>>`
- transpose bytes

Binary protocol
- bit flipping is lethal
- byte replacement (pick a byte, replace with attacker one)
	- edge case like 00,0f,7f are good ones

# Crash Control
- Direct registers
- double / N deref registers
- near one of the above

# Triage
- Reliability
- Constraints
- Repeatability
- What is controlled

# EBFE
- x86/x86_64


# Linux Memory Management
`Arena` - struct referencing 1+ heaps AND contains linked list of chunks within those heaps that are free

`Heap` contiguous region of memory divided into chunks for allocation

`chunk` contiguous section of memory that can be alloc/free or combined with adjacent chunks

Heap Exploit Vectors
- Overwrite linked list pointers for a write-what-where
- Overwrite heap metadata
	- Chunk header
	- Free list
	- look-aside list
- Overwrite structures
	- data
- Language specific
	- Dynamic control flow structures like dispatch tables and dynamically loaded fptrs
	- Object oriented vtable/vftable has function pointers to virtual methods of parent class (inheritance)
	- 


# Valgrind
- Walks next chunk and compares that size to next chunks prev_size

# Shellcode
- PIC
- bundled data must be located dynamically
call forward
- Needed in x86 because no reference to EIP
- unnecessary in x86_64
```asm
call fwd
db 'pwned234'

fwd:
pop <register> ; = address of 'pwned234'
; do stuff
```

jmp forward, call back (JMP, CALL. POP)
- Needed in x86 because no reference to EIP
- unnecessary in x86_64
```asm
bits 64

jmp fwd

back:
pop <register> ; = address of 'pwned'

fwd
call back
db 'pwned234'
```

floating point instructions
- Needed in x86 because no reference to EIP
- unnecessary in x86_64
```
fld st(1)
fnstenv [esp-0Ch]   ; writes three 8-byte fields, last of which is addr
					; esp-c = <1>, esp-8 = <2>, esp-4 <3>

pop <register> ; = address of 'fld st(1)'
```

IP relative
- x86_64 only
- similar in layout to jmp forward, call back (JMP, CALL, POP)

```
lea data(%rip), <register> ; location of data
; do stuff

data:
db 'pwned234'
```


# Egg hunter
- scan chunks individually
- scan segments linearly
- scan stack linearly
- use SIMPLE checksum to validate
	- possible for multiple copies, some corrupted
	- XOR, 16/32bit accumulator, ROL/ROR hash

# Cleanup
- process must survive and continue
	- thread MIGHT be able to die but verify how noticeable it is and side effects
- fix corrupted data structures
	- heap metadata, smashed object data
- fix control data
	- registers
	- VTables
	- other pointers
- unwind the stack to a known good point and continue from there?
	- choose deepest frame possible
	- resource leaks increase as frame gets shallower
		- released shared resources
		- restore register state for preserved registers
		- set appropriate return value
- exit thread/process
	- Hopefully inside dispatch thread or worker processes
	- does minimize cleanup
	- SHARED RESOURCES
		- repair shared data structures


# Linux System Programming
1. Source code written 
	- produces `main.c`
2. Preprocessor resolves preprocessor directives like `#include`, `#ifdef` and macros
	- produces `main.i`
	- `gcc -E main.c` -  Will only run the preprocessor
3. . Compiler converts source to assembly
	- produces `main.S`
	- `gcc -S main.c` Will only run preprocessor and assembler
4. . Assembler converts assembly to opcodes for arch
	- produces `main.o`
	- `gcc -c main.c` compiles but does NOT link
5.  Linker combines other objects from libraries used by project and resolves cross-object references
	- produces `a.out` or library `a.so/a.a`
	- gets symbols from other objects and static libraries
	- insert stubs for dynamic linking

# Make
goes to first line without a `.`
Specifies targets and their dependencies
Automatic variables

| Var | Meaning                                                       |
| :-- | :------------------------------------------------------------ |
| $@  | Filename of target of the rule                                |
| $<  | Name of first dependency in rules                             |
| $^  | All dependencies of rule                                      |
| $?  | Names of all dependencies in rules that are NEWER than target |
- Way more in Automatic Variables section of make manual

# Man pages

| man | tyoe                  |
| :-- | :-------------------- |
| 1   | general commands      |
| 2   | system calls          |
| 3   | C library             |
| 4   | drivers/special files |
| 7   | more details          |

# File API
`open()` returns a file descriptor number
- unbuffered
- usually no prefix on API calls e.g., (`open`, `printf`, `scanf`)
`fopen()` returns FILE*
- buffered
- relvant API usually starts with `f` e.g., (`fopen` `fprintf' `fscanf`)
`fdopen()` converts fd to FILE*

`flock` is BSD, not POSIX. Use `fcntl` instead

Run code on load or unload of a module
```
void on_load(void) __attribute__((constructor));
void on_unload(void) __attribute__(destructor));
```

# execve
has several pass-through API functions with different letters

| execve +        |                                                                               |
| :-------------- | :---------------------------------------------------------------------------- |
| l (lowercase L) | function takes program args in variable list format (like printf)             |
| v               | function takes program args in argv format                                    |
| p               | function will search for specified program like a shell ($PATH, curdir, etc)) |
| e               | function can be given a custom environment                                    |
# mulithreaded synchronization
Mutex -  One lock at a time, if lock in use sleep till system gives you access

Spinlock - Mutex-esque but without sleep. Use when tight loop costs more than waiting for resource

Semaphore - initialized to counter. If `ctr > 0` decrement and give access. Otherwise sleep and queue requestor

Condition variables + mutex allow safely checking for occurrence of some event
- lock with mutex, check/wait on condition variable 
- lock with mutex., set condition variable, wake threads with `pthread_cond_signal` for one or `pthread_cond_broadcast` for all waiting

Each can be used to ensure only a single thing is executing in the `critical section` (not to be confused with the mutex-esque Microsoft structure)

Use `volatile` on shared variables

gcc builtin atomics 
- `__atomic_fetch_sub`
- `__atomic_fetch_xor`
- `__atomic_add_fetch`
- `__atomic_store_n`
- `__atomic_load_n`

# I/O multiplexing and event loops
```
Wait on a set of fds
Wake up on alert or timeout
loop through fds with events and handle with NON_BLOCKING calls
sleep
```

epoll, select, poll
- libevent has user friendly wrappers
- See Linux System Programming binder

# Usermode CNO

inline asm `asm` (ANSI) or `__asm__` (GNU extension)

```
asm(   "asm1\n\t"
		"asm2\n\t" );
```

Extended
```

asm asm-qualifiers ( AssemblerTemplate
: OutputOperands
[ : InputOperands
[ : Clobbers ] ] )
```

extended GOTO
```

asm qualifiers ( AssemblerTemplate
:
: InputOperands
: Clobbers 
: GOTO labels)
```

- asm = `asm` (ANSI) or `__asm__` (GNU extension)
- asm-qualifiers
	- `volatile` - ASM has side effects
	- `inline` - small as possible and inline it
	-  `goto` - may perform a JMP
- AssemblerTemplate - String literal of assembly
- OutputOperands - CSV of C variables modified
- InputOperands - CSV of variables read
- Clobbers - CSV of registers and memory modified
- GotoLabels - CSV of C labels where asm can JMP

`%%eax` must double escape becuase thats also how token names are references `%[token_name]`

# Shellcode
 gcc with `nostdlib -pie -fPIE -fPIC`

# Loaders
- scan PH, map PT_LOAD, call entry point
- call linker for PT_DYN
- or use `memfd_create` to create a file in RAM (unless paged out) and pass it to `dlopen()`
- linker scripts (`ld --verbose` and `ld -T linkerscript`) are very useful to build blobs and specific formats
	- position sections in file and in virtual mem
	- discard sections
	- mark sections loadable/not
	- create positional vars for use by code (e.g., the start and end of a function for a checksum)
	- specify a format
	- allocate regions of memory
	- why do we care? remove sections that don't make sense, easil add new payloads and reference/use it

# Injection
`LD_PRELOAD` and `LD_LIBRARY_PATH`
- ignored in secure-exection mode
	- euid != uid
	- process with non-root UID executed with capabilities
	- Linux Security Model (LSM)
	- setuid binaries (unless LD_PRELOAD has no slashes is in standard dirs AND lib has suid bit enabled) (use rootkit to drop SO in a location if needed)
Modify ELF on disk and hijack function or start entry
- Re link ELF
- hijack unneeded section like `.note`
- hijack headers for unneeded section `.note`
- fit in dead space near end of binary/sections
- doesn't show up in disassembler
`/proc/<pid>mem` to write another virtual address space
- need `PTRACE_MODE_ATTACH_FSCREDS` for `/mem`
- need `PTRACE_MODE_READ_FSCREDS` for `/maps`
- typically only same user on defaults

`ptrace`
- `ptrace_attach`
- backup current state
- find a safe spot to clobber in .text (e.g., `glibc`'s unused `e_entry`)
- write code to map memory for so path
- `ptrace_cont` then break in again
- write code to setup dlopen call
- `ptrace_cont` then break in again
- write dlopen code
- `ptrace_cont` then break in again
- restore state, cleanup, `ptrace_detatch`


# Hooking
`.plt` - procedure linkage table
`.got` - global offset table
`.got.plt` section of GOT used by PLT
`.dynamic` - dynamic linking info
`.rela.dyn` - runtime/dynamic linking table
- info about vars that must be relocated when binary is loaded
`.rela.plt`
- relocation info about functions used

GOT
- Table of addresses stored in the data section (variables)
- `GOT[0]` has addresses of the `.dynamic` section
- `GOT[1]` has addresses of the link_map structure that contains info about dynamic linker
- `GOT[2]` holds address of dynamic linker code

PLT / GOT.PLT
- For functions
- Contains trampolines for code defined in dynamic libs
- `func@plt` is actually what is called
	- `plt` entry jumps to specified `got.plt` entry
	-  If not populated, this will be code to resolves symbol and updates GOT for next call
		- push `GOT[1]` the link map
		- jmp to `GOT[2]` which has actual dynamic linking code and populated GOT

# Hooking Targets
- replace GOT entry
	- will not work on explicit imports or saved off addresses
- C++ VTables
- VDSO overwrite
	- vsyscall without limitations on size, instructions removed, data moved to other pages, and trap code
- Callbacks
	- signal handlers
	- thread local storage
	- constructors/destructors
	- `.init` and `.fini`
	- `atexit`
		- not called on exec or if dead due to signal

# Hooking code
- placed at beginning of function (hot patching)
- x86_64 `E9` is relative jump within 2GB
	- otherwise move address into reg, and jmp/call reg
- trampoline at beginning saves off overwritten instructions, then jumps back to real function
	- filtering or modifying inputs
	- requires a LENGTH disassembler
- multi-stage patch
	- group of multiple patches to divert execution
	- use "safety instructions" like jumping to locations target modules
		- avoids heuristic detections
		- chain smaller trampolines to go longer distances than 2GB

# Hardening
strip symbols
avoid leaking info via strings
dynamic imports to glibc are good spots to hook and gives type info
- import by ordinal if possible to complicate RE a bit
run in weird locations
- `DT_INIT`
- `init`
- TLS callbacks
Deceive
- mix code and data can be a small delay
- imply a legit reason by mimicing a legit purpose
- high entropy code sticks out. Consider adding a reason
	- hash tables, compressed images, signed certs etc
Avoid common heuristics
- packet size/timing
- entropy of data/code
- side effects
- never send same payload twice
- mixin garbage or unrelated code
- functionaly equivalent instructions
techniques
- packing / compressing
- anti-disassembly
- anti-debug
	- do random stuff if detected debugging
	- `clone_untraced`
- static complexity
- overlapping instructions in variable sized instructions
- inline a bad opcode using `.byte 0xe9\n'
- indirect code
	- function pointers
	- signal handlers
	- call/jmp redirection at run time
- conditional calling conventions
- equivilent instructions
	- `movfuscator`
- checksum your code in separate process to detect `int 3`
- look for LD_PRELOAD in environment
	- remove it and exec a clean image
- look in `/proc/self/maps` for unexpected images
- `PR_SET_DUMPABLE` and `prctl`
- Make a VM or bytecode
- detect VM environment based on 
	- uptime
	- number of cores
	- special vm only instructions
	- loaded libraries
	- device ID and names
		- dmesg 
		- dmidecode

# Kernel CNO
`printk()` and dmesg

register for events using the notifier API
- timers, USN handler, disk injection, disk drive, thermal

Linux allows IRQs to be shared by multiple handlers
- `request_irq` must have `IRQF_SHARED` set
- #1 is keyboard
- ORRR you can modify non-shared to be shared without breaking anything...

Kernel iterates through each handler, passing the `dev_id` parameter used when registering with `request_irq`

Handling IRQs
- return `IRQ_HANDLED` is servicing, otherwise return `IRQ_NONE`
- obtain data from `in` and `out` instructions (`/arch/x86/include/io.h`)
- keyboard has mapping to `ioports` (see `/proc/ioports` as root)

## Notifier Chains
`/kernel/notifier.c`
`include/kernel/notifier.h`

Types
- atomic - execution in atomic or interrupt contect
- blocking - execution within process context, sleeping allowed
- SRCU - Sleepable Read-Copy-Update. Similar to blocking w/ different tradeoffs
- Raw - manage your own locking/blocking

`struct_notifier_block` 
- symbol to hook
- pre_handler fptr
- post_handler fptr
- fault_handler fptr

## Kprobes
API that allows hooked tracing through kernel
`include/linux/kprobes`
`struct kprobe`

## Tracepoints
programmer declared points that make callouts to tracers
`TRACE_EVENT` macro
can't use if not put in

## Ftrace
inserts call to `mcount()` at boot, a script records addresses, then replaces with NOPs for booting
`trace-cmd` is frontend to the tracing pseudo-filesystem


# ebpf
- allows programs to be inserted into and run by the kernel in light-weight VM
- x86_64 and ARM64 only
- groups with each new kernel version:
	- seccomp
	- socket_filter
	- net_filter
	- xt_bpf
	- cls_bpf


# Memory Management
Raw page table minipulation is "brute force" way

`task_struct.mm` (might be NULL if anonymous process, but `active_mm` will be present)
`task_stuct.active_mm`
 mm_struct has a member `struct vm_area_struct mmap`
- ordered by VA addr, low to high
- doubly linked list 
- nodes contain memory ingo

Attacking dynamic memory usually needs understanding of SLUB allocator
-  free list is a good overflow target

## Kernel Hooks
- hooking `sys_call_table` is VERY NOISY and often monitored
- get it's address, despite not being exported, with `kallsysm_lookup_name()`
- clear/set permissions on memory with
	- `write_cr0` and `read_cr0` from `/arhc/x86/include/asm/special_insns.h`
- on SMP disable preemption
	- `preempt_disable` and `preempt_anble`
- backup pointer and hook table

interrupt descriptor table
- single page of memory
- each entry called a "gate"
	- interrupt gate - usermode can't access (DPL field == 0) used for interrupts
	- system gate - usermode accessible (DPL == 3)
		- the 4 linux exception handlers associated with vectors 3,4,5,128 are activated by system gates
	- trap gate - not usermode accessible (DPL == 0) used by exception handlers

## hiding from tradition kernel lists
- `list_del_init(&__this_module.list);`
- `kobject_del(&THIS_MODULE->mkobj.kobj);`

# ASLR notes
kernel sometimes relies on certain fields at beginning or end of struct, but will randomize the other fields


# UEFI
- Allows system to perform code integrity checks of OS prior to boot
- Keys or certificates can be loaded for validation purposes of binaries

