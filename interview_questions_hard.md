Write post:
- What happens when a usermode process allocates large (4GB+) of memory
- What stops the alloc from being a DOS condition if the memory is allocated in a loop but never used
- How compiler for 32bit binary avoids stack overflow via canary and reordering stack
	- `-f-stack-protector-strong`