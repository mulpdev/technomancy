# Static Analysis of unknown binary
`file` to determine what format, architecture, debug symbols, stripped, static vs dynamic, interpreter, and kernel version

`binwalk`, unpackers, and decryption software (or hijacked decrypt functions)

`ldd` shows shared object dependencies for dynamic linked

`readelf -h` header info for more details than `file` gives
`readelf -s` print symbol table
`nm` prints symbol table with demangled names (C++)
`strings` and Mandiant's `floss` for obfuscated strings

`objdump -d <symbol>`, `objdump -D` - disassemble
- append `-M intel` to switch syntax
`objdump -f` file headers
`objdump -h` section headers

## Grep assistance
`| grep -B (num lines before) -A (num lines after)
`| grep --color -E "mov .+, r15" ropper.txt`

Diassemblers
Function signatures `FLIRT` and `Function ID / FiDB`
binary diffing

# Objdump disassemble flat file 
```
(target-triplet)-objdump -b binary -m (target arch from objdump) -i --endian={little|big} -M hex --adjust-vma=0xabcd1000 -D file.bin
```

# Dynamic Analysis of unknown binary
Use sandbox or safe VM/hardware

`strace` and `ltrace` 

`gdb` with pwndbg or GEF

`qemu` or `gdb server` 

In-memory unpackers

Advanced:
- `frida` and other instrumentation
- In memory patching or LD_PRELOAD of shared objects
- Proxying network traffic


# Fuzzing
`dumb vs smart` - Random bytes vs knowledge of format
`mutational vs generational` Modify existing data vs create it from scratch

Simple fuzz tactics
- Swap bytes
- MAX_INT, MIN_INT, -1, NaN
- Flip signs
- special characters in strings

Effectiveness
- speed
- code coverage
- number of crashes in some quantum of time
- actual bugs found
- can it find known bugs

Fuzzing Harness
- 3 parts: Mutation engine, process control and monitoring, and input/output between fuzzer and target

Advanced
- reducing input to minimum necessary to cause crash
- grouping related crashes
- core dumps along with input

# Source Code Audits
- Access to details and context not found in binary via structure and comments of source
- Look for places untrusted data comes in
- Identify and analyze relevant algorithms and data structures like allocators, JIT or scripting engines, garbage collection, etc
- Does similar code have multiple vulns?
- Is code well tested and used, or undocumented and edge case?
- What privileged areas are boundaries with code processing user input?
- Git commits message or comments with swearing, TODO/FIX/BUG/NOTE
- Commits after a long day when someone would be tired
- Profile the developers for buggy code and analyze their commits
- Check for Bug classes and
	- Off-by-one
	- missing default case
	- not checking return values
	- byte != char
	- Use `= & |` when they meant `== && ||`

# Weaponizing Exploit 
Continuity of execution
- Fixing stomped over memory
- graceful finishing of functions
- not crashing system when complete nor after payload delivery

Environment tolerance (memory state, load, network traffic, repeated exploitation attempts)

Obfuscate
- Alternative NOP instructions
- Trampolines over hardcoded addresses

Size
- Break it down into stages
- scatter components over multiple input points
	- Egg hunting might be useful
- use shorter instructions if possible
	- Thumb mode
- sign extend single byte values into larger ones
- construct data dynamically instead of pre-packing it

Input filters
- Avoid bad bytes / NULLS
	- Dynamically assemble code with bad chars
	- Use program behavior (strcpy appends nulls) to write bad chars
	- encode shellcode and force decoding before use if program allows

Avoid hardcoded
- PIC
- get IP via call/pop or FNSTENV
- Get addresses via scanning or walking tables
- Get addresses from nearby structures


# RE / Development

Hooking
- Overwriting pointers in jump/call tables
- detour patching / hot patching
- dependency injection
- patching
- modify disk image

Santization
- Stripping
- Static compilation to avoid FLIRT/FID
- inline functions
- inline string/data generation/deobfuscation

Obfuscation
- Weird names in byte code
- junk instructions
- executable code in data sections
- inlining
- self-modifying code
- dead code
- packing
- encryption/decryption of chunks only at use
- custom VMs
- deleting binary from disk at start
- jump to registers
- decoy or booby-trapped code

Breaking diassemblers
- Forcing linear disassemblers to lose their place on variable length instruction archs
- never taken branches to offsets
- violating formats
- interleaving code and data
- exploit the disassembler directly

Anti-debug
- isdebuggerpresent() and inlined version
- attached debugger to yourself (or ptrace)
- use of custom exception handlers + calling `int3`
- checking OS internals (registery, process info, other running processes)
- timing and environment checks (also for VM)
- integrity checks of code (particularly with encryption)


# Biblio

https://book.hacktricks.xyz/linux-hardening/linux-post-exploitation

Format String
- https://owasp.org/www-community/attacks/Format_string_attack

Double Free
- https://ir0nstone.gitbook.io/notes/types/heap/double-free

Serialization
- https://www.alertlogic.com/blog/writing-exploits-for-exotic-bug-classes-unserialize-d52/
- https://owasp.org/www-chapter-vancouver/assets/presentations/2020-05_Exploiting_and_Preventing_Deserialization_Vulnerabilities.pdf

NULL Pointer Deref
- https://fuzzysecurity.com/tutorials/expDev/16.html

Type Confusion
- https://www.microsoft.com/en-us/security/blog/2015/06/17/understanding-type-confusion-vulnerabilities-cve-2015-0336/
- https://socradar.io/understanding-the-type-confusion-vulnerability/

Double Fetch
- https://www.infosecinstitute.com/resources/hacking/exploiting-windows-drivers-double-fetch-race-condition-vulnerability/
- https://www.usenix.org/conference/usenixsecurity17/technical-sessions/presentation/wang-pengfei
- https://github.com/SouhailHammou/Drivers/tree/master/double-fetch-racecondition
- https://research.nccgroup.com/wp-content/uploads/2022/03/NCC_Group_Whitepaper_DoubleFetch2022_Report_2022-03-25_v1.0.pdf

Spraying
- https://duasynt.com/blog/linux-kernel-heap-spray
- https://duasynt.com/blog/linux-kernel-heap-spray
- https://www.corelan.be/index.php/2011/12/31/exploit-writing-tutorial-part-11-heap-spraying-demystified/
- https://www.analyticssteps.com/blogs/what-heap-spraying-attack (and p`revention)
- https://www.blackhat.com/docs/eu-16/materials/eu-16-Wen-Use-After-Use-After-Free-Exploit-UAF-By-Generating-Your-Own-wp.pdf

UBI
- https://www-users.cse.umn.edu/~kjlu/papers/tss.pdf
- https://www.usenix.org/system/files/woot20-paper-cho.pdf

Practice
- https://github.com/hacksysteam/HackSysExtremeVulnerableDriver


Models
- https://cwe.mitre.org/ Common Weakness Enumeration
- 