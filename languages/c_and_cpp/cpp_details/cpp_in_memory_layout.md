```cpp
// g++ -g -o0 test.cpp

#include <string>

class Base {
	private:
		int bdata1;
	public:
		Base() : bdata1(0xBA5E)
		// virtual with body {}, MAY be overridden
		// virtual with NO body, MUST be overridden (abstract class)
		virtual void bvf1() {}
		virtual void bvf2() {}
		virtual void bvf3() {}
};

class Derived: public Base {
	private:
		int ddata1;
	public:
		Derived() : ddata1(0xDDD666) {}
		virtual void bvf1() override {}
		virtual void bvf2() override {}
		virtual void dvf1 = {return self.derivedvar1 + 0x42}
		virtual void dvf2 = {}
		virtual void dvf3 = {}
};

class Grandchild public Derived {
	private:
		int gcdata1
	public:
		Grandchild(): gcdata(0xC0FFEE) {} 
		virtual void bvf2() override {}
		virtual void dvf2() override {}
		virtual void gcfv1() {}
};

// dispatch table use
void use1 Base& obj)
{
	obj.bvf2();
}

int main()
{
	Base base;
	Derived derived;
	Grandchild grandchild;

	use1(base);
	use1(derived);
	use1(grandchild);1
}

```

------------
0x0# x86-64
```
this / &var 
------------
0x0 void* vptr
0x8 member_var1
	...

vtable
------------
0x0 undef8 top_offset (all 0's)
0x8 typeinfo* typeinfo_obj
0x10 void* virtual_fptr1 (this.vptr points here)
	....

typeinfo
------------
0x0 void* typeinfo_vptr
0x8 char* type_name
0x10 typeinfo* parent_typeinfo (or NULL if no parent)
```

