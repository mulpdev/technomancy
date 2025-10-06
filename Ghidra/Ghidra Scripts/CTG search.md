CTG - Clang Token Group

# Recursive search for interesting func calls

```python
import sys
sys.path.append('/opt/ghidra_10.3_PUBLIC/')
from collections import OrderedDict

from ghidra.app.decompiler import DecompInterface
from ghidra.util.task import ConsoleTaskMonitor

def matchesAny(needles, code):
	if any(x.upper() in code.upper() for x in needles):
		return True
	return False
	
def recurseCTG(ctg, needle_bytes):
	ret = []
	
	if not hasattr(ctg, '__iter__'):
		# base case. Not iterable means ClangTokenGroup has no more children
		# We want the case immediately prior for the smallest complete string
		return None
	
	for c in ctg:
		if needle_bytes in c.toString():
			tmp = recurseCTG(c, needle_bytes)
			if tmp = None:
				ret.append(ctg) # ctg is the smalled complete CTG string
			else:
				ret = ret + tmp # combine lists
	return ret
	
def hasInterestingMath(code):
	MATH_LIST = ['+', '-', '*', '/', '%']
	DATA_LIST = ['g_', 'DAT_'] # indicates data or globals
	USER_LIST = ['var'] # uVar, iVar, etc	

	ret = True

	if findInStr(MATH_LIST, code)
		#if matchesAny(code, DATA_LIST): # Math on data reference
		if matchesAny(code, USER_LIST): # Math on potential user data
			return True
	return False

def filterCTGs(ctg):
	code = ctg.toString().decode('utf-8')
	
	# search parameters passed into function call
	try:
		params = code.split('(', 1) # params 0 will be function name, 1+ is "first param" etc
		# either check param[x] or all params
		
		# example, look for interesting math in param 2. memcpy(src, dst) means dst math might be arb write
		res = hasInterestingMath(param[2])
		return res
	except:
		pass
	return False

def get_code_addresses(ifc, function, needle_bytes):
	func_decomp = ifc.decompileFunction(function, 0, ConsoleTaskMonitor())
	ccode_markup = func_decomp.getCCodeMarkup()
	ctg = ccode_markup.getClangFunction()
	
	ctg_list = recurseCTG(ctg, needle_bytes) # ctg is ClangTokenGroup
	for ctg in ctg_list:
		if filterCTGs(ctg):
			tmp = (str(ctg.minAddress), str(ctg.maxAddress), str(ctg))
			ret.append(tmp)
	return ret

def main():
	########################
	# EDIT THESE VARS
	########################
	TO_FIND_BYTES = b'cpy'
	IN_FUNC_NAME = "BlahBlah"
	
	func_name = IN_FUNC_NAME
	function = getGlobalFunctions(func_name)
	if len(function) == 0:
		print("{} Not found in GlobalFunctions()".format(func_name))
		exit(1)
	elif len(function) != 1:
		print("{} found {} entries in Global Functions()".format(func_name, len(function)))
		for f in functions:
			print(f)
		exit(1)
	
	ifc = DecompInterface()
	ifc.openProgram(currentProgram)
	to_find = TO_FIND_BYTES
	addresses = get_code_addresses(ifc, function, to_find)
	for a in addresses:
		print("{} - {}: {}".format(a[0], a[1], a[2])

if __name__ == '__main__':
	main()
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