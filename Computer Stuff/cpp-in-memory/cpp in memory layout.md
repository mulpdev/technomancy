# biblio 
https://web.archive.org/web/20200806192448/https://vishalchovatiya.com/memory-layout-of-cpp-object/


# Memory Layout of Simple & Non-Polymorphic Object in C++
No virtual functions

```cpp
class X {
    int     x;
    float   xx;

public:
    X() {}
    ~X() {}

    void printInt() {}
    void printFloat() {}
};
```

```
      |                        |          
      |------------------------| <------ X class object memory layout
      |        int X::x        |
      |------------------------|  stack segment
      |       float X::xx      |       |   
      |------------------------|       |
      |                        |      \|/
      |                        |    
      |                        |
------|------------------------|----------------
      |         X::X()         | 
      |------------------------|       |   
      |        X::~X()         |       |
      |------------------------|      \|/
      |      X::printInt()     |  text segment
      |------------------------|
      |     X::printFloat()    |
      |------------------------|
      |                        |
```

#  Layout of an Object With Virtual Function & Static Data Member