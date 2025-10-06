
# Skeleton Starter

```python
from __future__ import print_function # must be first line

# Setup for pylance LSP
from ghidra import *
try:
    from ghidra.ghidra_builtins import *
    from ghidra.program.flatapi import * # should import flatapi stuff`
except:
    pass # will fail at run time but configures pylance

# Actual imports go here

# Globals
AF = currentProgram.getAddressFactory()
DTM = currentProgram.getDataTypeManager()
FM = currentProgram.getFunctionManager()
MEM = currentProgram.getMemory()
PL = currentProgram.getListing()
SM = currentProgram.getSymbolTable() # actually returns SymbolManager obj

# main
addr = AF.getAddress("1234ABCD")
```
# Python 2.7 isms
Jython = Python 2.7

```python
print("{} has no defined data".format(addr)) # Python2 format strings

print( "0x{:08x} >> 5 = {:08X}".format(addr, addr >> 5) ) # YES (x or X is valid)

somewidth=14
"Blah {:>{q}}".format(thing, q=somewidth)

print('{:>2} {:>2}'.format(i, hex(i)[2:])) # YES
print('{:02} {:02}'.format(i, hex(i)[2:])) # NO, can't use character error

# from __future__ import print_function allows
print("blah", end="") # no newline
```

Cheatsheet: https://learnpython.com/blog/python-string-formatting/

# Ghidra isms to be aware of

## `get*[At/Containing/Before/After](Address)`

```
0xDEADBEED 0x00 ushort MY_USHORT 
0xDEADBEEF 0x41 string MY_STR "ABC"
0xDEADBEF0 0x42
0xDEADBEF1 0x43

0xDEADC0DE 0x11 MY_FUNC1
0xDEADCADE 0x11 MY_FUNC2
```


`get*At()` Get info AT that exact address or None if address has nothing defined. Typically only the `Min` address has the metadata, others do not.
- Examples 
	- 0xDEADBEEF and 0xDEADCODE are tagged with info
	- 0xDEADBEF0 and 0xDEADCODF are NOT

`get*Containing()` handles the MIN address issue of `get*At()` 
- Conceptually similar to C macro `container_of`
- Examples 
	- 0xDEADBEFO is contained in OxDEADBEEF
	- 0xDEADCODF is contained in 0xDEADCODE

`get*[Before/After]()` will get prev/next info from given address
- Before *may* be the same as `get*Containing()` if address is a part of the same defined information
- Examples 
	- 0xDEADBEED is BEFORE OxDEADBEEF
	- 0xDEADCADE is AFTER 0xDEADCODE

# Ghidra pyi and LSP
https://github.com/VDOO-Connected-Trust/ghidra-pyi-generator

# Imports from rest of API
Note: pyi/LSP awareness not withstanding. Haven't figured that out completely 

Look at the top of the API web page and you will see the Java package and Java class names.  
```
Package ghidra.something.blah.stuff  # Python 'from'
Class JavaClass                      # Python 'import'
```

You will use these to import into Python
```python
		# Java Package                 # Java class
from ghidra.program.model.mem import MemoryAccessException
from ghidra.program.model.util import CodeUnitInsertionException
```

# Address
```python
AF = currentProgram.getAddressFactory()
```

## Getting addresses from Ghidra
```python
# Address from string
addr = AF.getAddress("1234ABCD")

# Adddress from int/long
x = 1024
addr = AF.getAddress(hex(x)) # remember, hex() makes a string

# Math
addr2 = addr.add(2048) 
addr2 = addr.sub(512)

# Printing
print("A single address {} can be double clicked".format(addr))
print("An address in a list {} can be double clicked".format([addr, addr]))

# From Data object
data.getMinAddress()
data.getMaxAddress()

# From Function object, via AddressSetView
asv = func.getBody()
asv.getMinAddress()
asv.getMaxAddress()
```

# Data
```python
PL = currentProgram.getListing() # or some in Flat API
```

Code is not data! Not usually an issue but some architectures mix them in memory

Only MIN address (i.e., `data.getMinAddress()`) of the data is tagged with metadata
	- e.g., `int/string/pointer/struct/etc` at 0xABCD1000 but `None` at 0xABCD1001
	- [see Ghidra isms to be aware of](#Ghidra-isms-to-be-aware-of)

```python
# Exactly at specific address 
data_at = PL.getDataAt(addr)
# Data that contains specific address
data_containing = PL.getDataAfter(addr)
# Data immediately before specific address (could be same as dataContaining() )
data_after = PL.getDataAfter(addr)
# Data immediately after specific address
data_before = PL.getDataBefore(addr)
```

# DataType

```python 
DTM = currentProgram.getDataTypeManager()
```

DataType Manager GUI is bottom left of code browser window by default. Name filter is at bottom of that panel.

## Getting DataTypes

```python
# get DataType from Data object
addr = AF.getAddress("DEADCODF") # not start of data
data = PL.getDataContaining(addr)
dt = data.getDataType()

# get DataType from DTM
out_list = []
DTM.findDataTypes("DT_NAME_HERE", out_list) # no globbing, matches exact string

case_sensitive = False
DTM.findDataTypes("DT*_HER?", out_list, case_sensitive, getMonitor()) # globbing

# Arrays
from ghidra.program.model.data import ArrayDataType
dt = getDataTypes("dt_that_exists") # Flat API
adt = ArrayDataType(dt, cnt, dt.getLength())
data = createData(addr, adt)
```

## Clearing/Setting Data Types
Clearing
```python
dt = PL.getDataAt(addr).getDataType()
clearListing(addr, addr.add(dt.getLength()))
```

Setting
```python
new_dt = getDataTypes("SomethingElse")[0]
data = createData(addr, new_dt)
```

Must clear data type to avoid `CodeUnitInsertionException` when setting!!!
	- see `from ghidra.program.model.util import CodeUnitInsertionException`
	- says "Conflicting data exists at address X to Y"

 try/except for illustration purposes of defensive programming
```python
from ghidra.program.model.util import CodeUnitInsertionException

old_dt = PL.getDataAt(exists_with_dt_addr).getDataType()
clearListing(addr, addr.add(old_dt.getLength()))

new_dt = getDataTypes("Exists")[0]

try:
	data = createData(exists_with_dt_addr, new_dt) # Success
except CodeUnitInsertionException:
	print("Should not hit this if properly cleared")
```

## Searching for a Data Type
```python
def find_data_types:
    case_sensitive = False
    outlist = []
    #DTM.findDataTypes("DT_NAME_HERE", outlist) # no globbing
    DTM.findDataTypes("DT*_HER?", outlist, case_sensitive, getMonitor()) # globbing in name

    dt_names = []
    for dt in sorted(outlist):
        name = dt.getName()
        dtnames.append(name)
```

## python isInstance() with Ghidra types
```python
glob = 'ABC_*_DEF*_t'
out_list = []
case_sensative = False
DTM.findDataTypes(glob, out_list, case_sensitive, getMonitor())

for dt in out_list:
	if isInstance(dt, ghidra.program.database.data.StructureDB):
		# ...SNIP...

```
## Arrays
Arrays are named as `DataType[cnt]`
- Each new cnt is a new Array DataType. 
- It's possible that `getDataTypes(DataType[cnt-1])` may not exist

```python
# Arrays
from ghidra.program.model.data import ArrayDataType
dt = getDataTypes("dt_that_exists") # Flat API
adt = ArrayDataType(dt, cnt, dt.getLength())
data = createData(addr, adt)
```

## getComponent()
DATA.getComponent() returns DATA
STRUCTURE.getComponent() returns DATA TYPE COMPONENT

```python
def get_element_from_array(addr, index):
    myarray = PL.getDataAt(addr)
    data = myarray.getComponent(index)
    dt = data.getDataType() 

def get_element_from_struct(addr, index):
    mystruct = PL.getDataAt(addr)
    data_component = mystruct.getComponent(index)
    data - data_component.getDataType()
```

## Datatype Mnemomics
```python
# dt.getMnemonic(data.getDefaultSettings()) = `u'addr[10]'` pretty damn close, prob just a decode() away

'''
If an address has multiple data types (array of structs, struct[0], struct0.field0_0x0, ...), drill down to the DT you want
'''
def get_specific_datatype_from_multiple(addr, target_data_str = ''):
    # tmp will be a DataDB then DataComponent for rest of loop
    tmp = PL.getDataAt(addr) # uses getDataAt() not getDataContaining() to not change examined address

    while tmp is not None:
        dt_mnem = tmp.getDataType().getMnemonic(tmp.getDefaultSettings()) # getDefaultSettings works for DataDB and DataComponent
        if data_mnem.lower() == target_dt_str.lower():
            return (curr_data, i) # call getComponent(0) i times on same nested type to get same result
    return None
```

# Scalars
Getting the value of something may not give an actual value, but a Scalar. You can do simple math and bit math. 

```py
.getUnsignedValue()
.getSignedValue()
```

# Structs
### Generically getting arbitrary scalar from a struct
TODO: Fixup
```python


```
# Functions
```python
FM = currentProgram.getFunctionManager() # or some in Flat API
```

Only entry point address (i.e., `func.getEntryPoint()`) of the func is tagged with metadata
	- [see Ghidra isms to be aware of](#Ghidra-isms-to-be-aware-of)

```python
# Exactly at specific address 
func_at = FM.getFunctionAt(addr)
# Func that contains specific address
func_containing = FM.getFunctionAfter(addr)
# Func immediately before specific address (could be same as functionContaining() )
func_after = FM.getFunctionAfter(addr)
# Data immediately after specific address
func_before = FM.getFunctionBefore(addr)
```

Get/Set Params
```python

params = func.getParams()

# Set Params
from ghidra.program.model.symbol import SourceType
from ghidra.progra.model.listing.Function import FunctionUpdateType
params[i] = setDataType(target_dt, SourceType.ANALYSIS/USER)
force = False
func.replaceParameters(params, FunctionUpdateType.DYNAMIC_STORAGE_FORMAL_PARAMS, force, SourceType.ANALYSIS/USER)

```

```python
func.getName()

forward = True
fList = FM.getFunctions(forward)
for f in fList:
	fname = f.getName()
```

# Comments
- CodeUnit class has the comment type enum, even if target address not a CodeUnit
- Parameter order is not consistent in these functions
```python
from ghidra.program.model.listing import CodeUnit

PL = currentProgram.getListing()


PL.getComment(CodeUnit.POST_COMMENT, addr)
PL.clearComments(addr_start, addr_end)
PL.setComment(addr, CodeUnit.POST_COMMENT, "This is the comment")
```

# Transactions
https://github.com/GhidraJupyter/ghidra-jupyter-kotlin/issues/19#issuecomment-946632900

```kotlin
import ghidra.program.model.listing.CodeUnit as CodeUnit

var id = currentProgram.startTransaction("Set line comment from Kernel")

var listing = currentProgram.getListing()
listing.setComment(toAddr(0x0), CodeUnit.PLATE_COMMENT, "Hello Ghidra")

currentProgram.endTransaction(id)
```

or even more specific:

```kotlin
import ghidra.program.model.listing.CodeUnit as CodeUnit

var listing = currentProgram.getListing()

var id = currentProgram.startTransaction("Set line comment from Kernel")

listing.setComment(toAddr(0x0), CodeUnit.PLATE_COMMENT, "Hello Ghidra")

currentProgram.endTransaction(id)
```


# Snippets Below Here -----------------------------
# Converting signed int to an unsigned hex string
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


# getting Pointer at location
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

# Get address blocks corresponding to a string matching part of a decomp

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

# Get value by symbol name
```python
SM = currentProgram.getSymbolTable()
PL = currentProgram.getListing()

symname = 'symbolname'
syms = SM.getGlobalSymbols(symname) # returns array
sym = syms[0]

data1 = PL.getDataAt(sym.getAddress())
print('{} {}'.format(data1.getDataType(), data1.getValue()))

# If it's a nested structure
# e.g., 0xdeadbeef == a label, an array, and the zeroth element
data2 = data1.getComponent(index)
print('{} {}'.format(data2.getDataType(), data2.getValue()))

data3 = data2.getComponent(index)
print('{} {}'.format(data3.getDataType(), data3.getValue()))

```

# Get symbol of pointer
```python
def get_symbol_of_pointer_member():
    out = []
    data = PL.getDataAt(data_addr)
    for i in range(data.getNumComponents):
        member = data.getComponent(i)
        addr = member.getValue() # Will be address, not scalar
        symbol = SM.getPrimarySymbol(addr)
        symname = symbol.getName()
        out.append(addr, symname)
```

# Make pretty clickable table
```python
class MyRowClass(AddressableRowObject)
	def __init__(a,b):
		self.a = a
		self.b = b
	def getThingA(self):
		return self.a
	def getThingB(self):
		return self.b
		
class MyColClass(StringColumnDisplay)
	def getColumnName(self)
		return "Column Name Here"
	def getColumnValue(self, row)
		return row.name

#syms = interesting symbols
rows = []
for sym in syms:
	try:
		f = getGlobalFunctions(sym)[0]
		xrefs = getReferencesTo(f.getEntryPoint())
		for xref in xrefs:
			xaddr = xref.getFromAddr()
			xfunc = fm.getFunctionContaining(xaddr)
			rows.append( MyRowClass(xfunc.getName(), xfunc.getEntryPoint())

table = createTableChooserDialog("TitleBarString", None)
table.addCustomColumn(MyColClass())
for o in out:
	table.add(o)

```

# Get XRefs chain backwards

```python
MAX_DEPTH = 5
RESULTS = set()

def dfs(addr, level, xrefs_chain):
	if not addr or level > MAX_DEPTH:
		RESULTS.add(xrefs_chain)
		return

	prefix = ''
	if level > 0:
		prefix = ' <- '

	container_addr = None
	container = getFunctionContaining(addr)

	if container:
		container_addr = container.getEntryPoint()
		xrefs_chain += '{}{}()@ {}'.format(prefix, container, container_addr)
	else:
		container = getDataContaining(addr)
		container_addr = container.minAddress
		xrefs_chain += '{}{}@ {}'.format(prefix, container, container_addr)
	
	for r in getReferencesTo(container_addr):
		from_addr = r.getFromAddress()
		dfs(from_addr, level+1, xrefs_chain)

dfs(getCurrentAddress) # from cursor
for res in RESULTS:
	print(res)
```

# Get all functions
```python
fm = currentProgram.getFunctionManager()
fList = fm.getFunctions(True)
for f in fList:
	fname = f.getName()
```

# Function Prototypes
https://www.reddit.com/r/ghidra/comments/1205pnt/python_api_apply_function_prototype/

```python
from ghidra.app.cmd.function import ApplyFunctionSignatureCmd
from ghidra.program.model.symbol import SourceType

func = createFunction(address, "function_name")
sig = getDataTypes("signature_name")[0]
cmd = ApplyFunctionSignatureCmd(func.getEntryPoint(), sig,SourceType.USER_DEFINED)                                                                                                          
runCommand(cmd)
```

# Find destinations for a call instruction
```python
def find_possible_destinations_of_inst(inst):
    '''
    walk back a few instructions looking for refs to some data or func

    TODO: Fix assumptions
    - first ref may not be the destination
    - refs may not be data only, it might be a func
    '''
    prev_inst = inst
    for i in range(5):
        prev_inst = prev.getPrevious()
        for ref in prev_inst.referencesFrom(): # "references FROM instruction to something else
            dst_addr = ref.getToAddress()
            data = PL.getDataContaining(dst_addr)
            return data.getMinAddress()
    return None

def find_calls_to_dst_func(func, dstfunc):
    ret = []
    addr_set_view = func.getBody()
    inst = PL.getInstructionAt(addr_set_view.getMinAddress())

    while addr_set_view.contains(inst.getAddress()):
        mnem = inst.getMnemonicString()
        if 'call' = mnem.lower():
            dst_addr_set_view = dstfunc.getBody()
            for ref in inst.getReferencesFrom():
                to_addr = ref.getToAddress()
                if dst_addr_set_view.contains(to_addr):
                    ret.append(ref.getFromAddress())
        inst = inst.getNext()
    return ret

def find_instruction_in_func(func, target_mnemonic="call"):
    addr_set_view = func.getBody() # address range of function
    inst = PL.getInstructionAt(addr_set_view.getMinAddress())

    while addr_set_view.contains(inst.getAddress())
        mnemonic = inst.getMnemonicString()
        if mnemonic == target_mnemonic:
            return find_possible_destinations_of_instruction(inst)
```

# Get data referenced by instruction
```python
def get_data_referenced_by_instruction(addr):
    inst = PL.getInstructionAt(addr)
    refs_from = inst.getReferencesFrom()
    ref_to_addr = refs_from[0].getToAddress()# hopefully only 1 ref
    return ref_to_addr

```

# Find use as parameter
```python
def find_uses_as_parameter(target_addr):
    ret = []
    from ghidra.program.model.symbol import RefType
    for ref in getReferencesTo(target_addr):
        if ref.getReferenceType() != RefType.PARAM:
            continue
        ret.append(ref)
    return ret

```