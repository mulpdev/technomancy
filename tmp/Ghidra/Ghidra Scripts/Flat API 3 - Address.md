
## Creating and Manipulating Addresses
```python
AF = currentProgram.getAddressFactory()

addr = AF.getAddress("1234ABCD")

x = 1024
addr = AF.getAddress(hex(x))
print("{} is double clickable".format(addr))

addr2 = addr.add(2048) 
addr2 = addr.sub(512)
```

## Extracting from Data
- Data Type has address MEMBER VARIABLES (not funcs) and `minAddress` is typically the address you want
```python
data.minAddress
data.maxAddress
```

## Extracting from Memory Map
- No min/max Address, but can `getFirstRange()` and `getLastRange()`
- Address iterator
- Find bytes
```python
MEM = currentProgram.getMemory()

if mem.contains(addr):
	pass
else:
	print("{} not in memory map".format(addr))
```

## Converting signed int to an unsigned hex string
- Python ints are signed numbers. hex() will return -0xABCD sometimes

```python
def twosCompliment(num):
	return (num ^ 0xFFFFFFFF) + 1 # Use appropriate width max here if not 32bit

def signed_num_to_unsigned_hexstr(num):
	if num < 0:
		twosComp = twosCompliment(num) 
		ret = hex(twosComp)[:-1] #strip off trailing L
		if hexstr[0] = '-': # remove negative sign
			ret = ret[1:]
		return ret
	else:
		return hex(num)	
```


## getting Pointer at location
- requires `signed_num_to_unsigned_hexstr()` from above
```python
from ghidra.program.mem import MemoryAccessException

def getPointerAt(addr):
	i = getInt() # MemoryAccessException
	h = signed_num_to_unsigned_hexstr(i)
	ptr AF.getAddress(h)
	return ptr

ptr = getPointerAt(addr) # deref addr *
ptr2 = getPointerAt(ptr) # deref addr **
```

Get address blocks corresponding to a string matching part of a decomp

```python
from ghidra.app.decompiler import DecompInterface
from ghidra.util.task import ConsoleTaskMonitor

def recurse_search(ctg, query):
	ret = []
	
	# base case is an element that has no children
	if not hasattr(ctg, '__iter--'):
		return None

	for c in ctg:
		if query in c.toString():
			tmp = recurse_search(c, query)

			if tmp == None: # base case
				ret.append(ctg) # we want the full code, not just one part
			else:
				ret = ret + tmp 

	return ret

program = getCurrentProgram()
ifc = DecompInterface()
ifc.openProgram(program)

query = 'void* '
funcname = 'main'
function = getGlobalFunctions(funcname)[0] # assume one match

# decompile the function
results = ifc.decompileFunction(function, 0, ConsoleTaskMonitor())
markup = results.getCCodeMarkup()

#ctg = ClangTokenGroup
ctg = cmarkup.getClangFunction()

hits = recurse_search(ctg, query)
for hit in hits:
	print(hit.minAddress)
	print(hit.maxAddress)

```