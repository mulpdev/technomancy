## Get value at symbol
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

## List all functions
```python
fm = currentProgram.getFunctionManager()
fList = fm.getFunctions(True)
for f in fList:
	fname = f.getName()
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