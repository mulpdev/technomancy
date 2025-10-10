```python
# Get a list of ghidra.program.model.lang.Register
currentProgram.getProgramContext().getRegisters()

# Update decomp

y = ghidra.program.model.pcode.HighFunctionDBUtil
y.updateDBVariable()
-register or address
```

`@` tags
```python
#First line of file is the description
#@category
#@author
```

```python
# returns MemoryBlock containing address or Null (None?) if nothing contains addr
FlatProgramAPI.getMemoryBlock(Address)
```