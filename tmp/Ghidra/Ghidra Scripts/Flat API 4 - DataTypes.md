DataType Manager is bottom left of code browser window by default

## `getDataTypes()`
- Returns a DataType object that matches the name in the string, or None
- Data Type Manager is bottom left of CodeBrowser window by default
	- Name of Data Type is in this tree and can be filtered at bottom
```python
dt_abc = getDataTypes("Abc")[0]
```

## `getData*()`
- NOTE: Only first address of some data is tagged with the data type
	- e.g., `Pointer/struct/etc` at 0xABCD1000 but `None` at 0xABCD1001
	- code is not data!
- `getDataAt()` will return None if address has no defined data
- `getDataContaining()` handles the issue in bullet 1
- `getData[Before/After]()` will get prev/next data type from given address
	- Before *may* be the same as `getDataContaining()`
```python
PL = currentProgram.getListing()

data = PL.getDataAt(addr)
try:
	dt = data.getDataType()
except AttributeError:
	print("{} has no defined data and getDataAt() returned None".format(addr))
```

## Setting/Clearing Data Types
Setting
```python
new_dt = getDataTypes("SomethingElse")[0]
data = createData(addr, new_dt)
```

Clearing
```python
dt = PL.getDataAt(addr).getDataType()
clearListing(addr, addr.add(dt.getLength()))
```

Must clear data type to avoid `CodeUnitInsertionException`
	- Exception states "Conflicting data exists at address X to Y"
```python
from ghidra.program.model.util import CodeUnitInsertionException

dt = PL.getDataAt(addr).getDataType()
new_dt = getDataTypes("SomethingElse")[0]

# BAD
try:
	data = createData(addr, new_dt)
except CodeUnitInsertionException:
	print("Must clear datatypes in conflicting range")

# FIXED
clearListing(addr, addr.add(dt.getLength()))
data = createData(addr, new_dt) # Success
```

## Arrays are named as `DataType[cnt]`
- Each new cnt is a new DataType. It's possible that `getDataTypes(DataType[cnt-1])` may not exist
```python
from ghidra.program.model.data import ArrayDataType

dt = getDataTypes(SomeStruct)
adt = ArrayDataType(dt, cnt, dt.getLength())
data = createData(addr, adt)
```

