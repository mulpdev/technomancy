cmake is complicated

# simple tl;dr to try
- Not optimized and nuclear

common/conventional setup is
- `CMakeLists.txt` is in {project_root}
- `build/` is in {project root} and the current working directory for building

in project root:
```sh
# NUKE existing build artifacts
rm -rf build/  

# Using common convention...
mkdir -p build
cd build/

# pass path to top-level CMakeLists.txt to cmake (likely {project_root})
cmake ..

# do the build
cmake --build .
```

# CMakeCache errors


```sh
CMake Error: The current CMakeCache.txt directory path1/to/CMakeCache.txt is different than the directory path2/to/build where CMakeCache.txt was created. This may result in binaries being created in the wrong place. If you are not sure, reedit the CMakeCache.txt
CMake Error: The source "path3/to/CMakeLists.txt" does not match the source "path4/to/CMakeLists.txt" used to generate cache.  Re-run cmake with a different source directory.
```
- The project directory was moved or renamed after CMakeCache was built. 
- Fix by deleting the cache
```sh
find build/ -name "CMakeCache.txt" -exec rm {} \;
```



```sh
~/code/neovim$ make install  
mkdir -p ".deps"  
/usr/bin/cmake -S /home/user/code/neovim//cmake.deps -B ".deps" -G "Unix Makefiles"      
CMake Error: Error: generator : Unix Makefiles  
Does not match the generator used previously: Ninja  
Either remove the CMakeCache.txt file and CMakeFiles directory or choose a different binary directory.  
make: *** [Makefile:115: build/.ran-deps-cmake] Error 1
```
- Build requires ninja, but it wasn't installed so deps defaulted to Makefile
- Fix by deleting deps folder from build
```sh
rm -rf .deps/ 

# --OR--

rm -rf build/.deps/
```
