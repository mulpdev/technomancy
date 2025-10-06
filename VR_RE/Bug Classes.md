

# Bug / Vulnerability classes
`Command Injection` -  Unsanitized user input into a call to another binary (eg., `system()`) where expected data is followed by some control flow character(s) `; && ||` and further commands. Many `ping` commands in IoT devices have this
``
`Memory Overflow` - corruption of stack or heap memory by improper or lack of bounds checks around `strcpy` and `memcpy` families of functions. 
- Safe versions of these functions can be abused if attacker controlled input is used to determine size.
- Heap overflow can overwrite heap metadata allowing control or abuse when unlinking aka `write-4`!
- Header cookies, XOR of header metadata or moving metadata elsewhere, and implementing a safe unlink that validates chain are protections from heap overflows

`Format String` - Unsanitized user input into format strings. By appending format string variables you can produce reads by printing pointers/data or writes to pointers with `%n` (number of bytes written)
 - Using stack values `pops` them so walking the stack and using `%n` is feasible

`Integer conversion`
- `signedness` differences used in logic checks like a large uint value being interpreted as a signed int
- `size` differences when variables are truncated or expanded implicitly
- `overflow/underflow`

`Logic bugs` - trusting incoming data, bad cryptographic implementations, bad conditionals or boundary checking of values, bad assumptions of trust or control or assumed prior checks
- parameter manipulation to change price, or reset someone else's password
- manually entering a URL to a privileged page that assumes login checks already done

`Data Serialization` - Constructing a valid serialized object that when deserialized will inject data, corrupt memory, access/modify values normally not accessible etc.
- Not limited to protocol/defined serialization like JSON, XML, Python Pickle, language objects (Java, .NET) but also for custom formats like customer protocols, license and key files, etc

`Use After Free / UAF` - Object/struct deallocated but pointer is still usable (not NULL). Allocator can reallocate that block and the dangling pointer now points to new object. The new data can either be
-  genuine, and the dangling pointer will be used to leak it
-  attacker controlled and pointer can be used to access tainted data
- mulithreaded apps commonly have UAF (ACTP)

`Null Pointer Deref` - Through normal logic a pointer to object/struct is set to NULL (e.g., in response to an error condition), BUT subsequent code does NOT check for condition/response and uses the now NULL pointer to data. Controlling the page at NULL allows exploitation of this condition by faking the object/struct data in some tainted way (fptr, bad data etc)

`TOCTOU` - `Time of check Time of use` race conditions where check occurs, a malicious condition occurs on checked data, then the use occurs with now tainted data (filesystem, code with significant prechecks, transcation based code,  )
- Ex. `Double Fetch` a value is read, changed by another thread, and then read again without confirming the value/state from first check. Dangerous in kernels with pointer values
- File system access checks before IO

`Type Confusion` - (JS and interpreted languages?) When code logic expects one data type, but gets another.  Executes will occur with expectations/use/assumptions of the first datatype, of which the alternative data type may be crafted to abuse

`Double Free` - Calling `free()` more than once on the same pointer. First call is normal, subsequent calls will corrupt the allocator's data structures, specifically for the free list.
Allocating the same size chunk and writing to it will give some control over the free list structures, which can be abused to control the address of next allocation. 
- Specifics require understanding allocator's algorithm and data structures
- In C this is undefined territory 

`Use Before Initialization / UBI` - A value is not given at initialization. If the data can be influenced or controlled before use of that variable, it may lead to attacker control. Alternatively, if the memory was previously used by the kernel or secure code, it's use may to leak information
- Time consuming to generically exploit
- Does provide potential avenue to bypass Kernel ASLR see: CVE-2018-11508