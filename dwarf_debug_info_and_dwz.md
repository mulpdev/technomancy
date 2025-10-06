`sudo apt install dwz`
# Single Binary Single dwz

- main.cpp
- rectangle.h

```
g++ -gdwarf -o test main.cpp rectangle.h
cp test test.debug
strip --only-keep-debug test.debug

dwz -o test.dwz test.debug

strip --strip-debug --strip-unneeded -o test.strip test

eu-unstrip test.strip test.dwz -o test.undwz

file yay*
md5sum yay*
```

# Multiple Binary Single dwz
- main2a.cpp
- main2b.cpp
- rectangle2.h

```
g++ -gdwarf -o test2a main2a.cpp rectangle2.h
g++ -gdwarf -o test2b main2b.cpp rectangle2.h

cp test2a test2a.debug
cp test2b test2b.debug

strip --only-keep-debug test2a.debug
strip --only-keep-debug test2b.debug

dwz -m test2both.dwz test2a.debug test2b.debug

strip --strip-debug --strip-unneeded -o test2a.strip test2a
strip --strip-debug --strip-unneeded -o test2b.strip test2b

eu-unstrip test2a.strip test2both.dwz -o test2a.undwz
eu-unstrip test2b.strip test2both.dwz -o test2b.undwz

file test*
md5sum test*
```