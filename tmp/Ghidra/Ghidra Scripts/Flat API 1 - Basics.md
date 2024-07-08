## Useful Constants
```python
AF = currentProgram.getAddressFactory()
PL = currentProgram.getListing()
MEM = currentProgram.getMemory()
SM = currentProgram.getSymbolTable() # returns class SymbolManagercurrent function
FM = currentProgram.getFunctionManager()
```

## Python2 print format
```python
print("{} has no defined data".format(addr)) # Python2 format strings
```

## Imports from rest of API
```python
from ghidra.program.model.mem import MemoryAccessException
from ghidra.program.model.util import CodeUnitInsertionException
```
