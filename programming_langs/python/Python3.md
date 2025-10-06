# venv
```
python3 -m venv .venv/
./.venv/bin/python3
./.venv/bin/python3.11
./.venv/bin/pip
./.venv/bin/pip3
```

# List Comprehension

```python
>>> l = ["apple", "BANANA", "Cherry"]
>>> " ".join(l)
'apple BANANA Cherry'
>>> " ".join([x.capitalize() for x in l])
'Apple Banana Cherry'
>>> " ".join([x for x in l]).title()
'Apple Banana Cherry'
```

# String Formatting Hex
 
```python
>>> value = 42
>>> width = 6 # INCLUDING ANY PREFIX!
>>> f"{value:#0{width}x}"
>>> '0x002a'

'''
f - python3 f-string
" - Open quote
{ - start (first) variable substitution
value - variable name to sub in
: - add formatting for subbed variable
# - use the "0x" prefix
0 - fill with zeroes ...
{ - start (second) variable substitution
width - variable name to sub in
} - end (second) variable substitution
x hex digit case; lowercase (x) or uppercase(X)
} - end (first) variable substition
" - close quote
'''
```

# packages and importing

```
.
├── main.py
├── packA
│   ├── __init__.py
│   ├── moduleA.py
├── packB
│   ├── __init__.py
│   ├── moduleB.py
│   ├── packC
│   │   ├── __init__.py
│   │   ├── moduleC.py
```

If a directory contains a `__init__.py` file, it's treated as a package. This file must import the modules it wants to expose to anyone import the package.

If the `__init__.py` file is empty, or otherwise contains no import statements then resulting import will be empty, EVEN IF the module source files exist!
```python
import packA
>>> print(dir(packA))
'__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__path__', '__spec__']

>>> packA.moduleA
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: module 'packA' has no attribute 'moduleA'

```

If the `__init__.py` file contains imports (for example, the supplied moduleA), then resulting import will contain moduleA
```python
>>> import packA

>>> print(dir(packA))
['__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__path__', '__spec__', 'moduleA']

>>> packA.moduleA
<module 'packA.moduleA' from '/home/user/code/lsp-testing/py/packA/moduleA.py'>

>>> packA.moduleA.p1_print(2)
p1_print(): 2**2 is 4; 7 1337
4
```

find . -name "__pycache__" -exec rm -rf {} \;