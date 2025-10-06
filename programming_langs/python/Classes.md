
```python
class MyClass(MyParent1, MyParent2):
	def __init__(self, foo):
		self.foo = foo
	
	def func(self):
		pass
```

# Classes and static methods
from PyDocs
```
Transformation   Called froom an obj  Called from a class
function         f(obj, *args)        f(*args)
@staticfunction  f(obj, *args)        f(*args)
@classmethod     f(obj, *args)        f(*args)

obj CAN be "self"
```

```python
class MyClass(MyParent1, MyParent2):
	static_attrs = ["foo", "bar", "baz"]
	
	def __init__(self, foo, bar):
		self.foo = foo
		self.__dict__['foo'] = foo
		object.__setattr__(self, 'foo', foo)
		setattr(self, 'foo3', foo+3)
		
		self.__dict__=dict.fromkeys(['foo', 'bar'])
		self.foo = foo
		self.bar = bar
	
	def get_class_name_str(self):
		# Python 2 and 3 "old-style classes"
		print(f'self.__class__.__name__ = {type(self).__name__}')
		
		# Python 3 "new-style classes"
		print(f'type(self).__name__ = {type(self).__name__}')
		print(f'D.__name__ = {D.__name__}')
	
	'''
	CAN be called via instance e.g., self.method()
	CANNOT be called by MyClass.method()
	'''
	def method(self, x):
		pass
	
	'''
	CANNOT be called via instance e.g., self.undecorated_static_method()
	CAN be called by MyClass.undecorated_static_method()
	'''
	def undecorated_static_method()(a, b):
		pass
	
	'''
	CAN be called via instance e.g., self.static_method()
	CAN be called by MyClass.static_method()
	'''
	@staticmethod
	def static_method(a, b):
		pass
		
	'''
	CAN be called via instance e.g., self.class_method()
	    - BUT does not have access to instance members!
	CAN be called by MyClass.class_method()
	'''
	@classmethod
	def class_method(cls, b):
		pass
```

# @classmethod

```python
'''
#classmethod behavior is when method needs a CLASS reference but not access to the member variables. Pydocs example is dict class creating a new dict from an iterable
'''
#d = dict.fromkeys(cls, iterable, value=None)
>>> d = dict.fromkeys('abracadabra')
>>> d
{'a': None, 'b': None, 'r': None, 'c': None, 'd': None}
>>> d = dict.fromkeys(['abracadabra', 'alakazam'])
>>> d
{'abracadabra': None, 'alakazam': None}
```