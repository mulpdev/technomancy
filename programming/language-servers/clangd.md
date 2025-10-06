Using clangd as an LSP requires some effort to allow navigation. 

Derived from: https://neovim.discourse.group/t/clangd-lsp-jump-to-definition-takes-me-to-h-file/2153/2

clangd can parse different files for LSP functionality
- `.clangd` (or `compile_flags.txt`) has NO MAPPING of .h to .c
	- BOTH `vim.lsp.buf.declaration()` and `vim.lsp.buf.definition()` to the included header file of the symbol
	- see [[#Option 1 simple .clangd for single file project]]
	
- `compile_commands.json` maps .h to .c files
	- `vim.lsp.buf.declaration()` to points to the header file and `vim.lsp.buf.definition()` to points to the c file.
	- see [[#Option 2 compile_commands.json]]

# Option 1 simple .clangd for single file project
Source: https://medium.com/unixification/how-to-setup-clangd-with-gcc-headers-and-neovim-lsp-for-competitive-programming-2f3f98425ae1

The source uses `- -I/usr/include/c++/12` but I changed to use the array syntax `[ "-I/usr/include/c++/12", ]`

See [[#add system headers with gcc-ver]] for how to get the list of headers

```
CompileFlags:
    # gcc-12 -E -v -xc++ /dev/null (must use gcc-<ver>)
    # entries under: #include "..." search starts here:
  Add: [
    "-I/usr/include/c++/12",
    "-I/usr/include/x86_64-linux-gnu/c++/12",
    "-I/usr/include/c++/12/backward",
    "-I/usr/lib/gcc/x86_64-linux-gnu/12/include",
    "-I/usr/local/include",
    "-I/usr/include/x86_64-linux-gnu",
    "-I/usr/include",
    "-I./inc", # CHANGEME
  ]

Index:
  Background: Skip
```

# Option 2 compile_commands.json
CMake can natively generate this file
make cannot, but the program `bear` can parse a Makefile and generate it

## Generation with CMake
If your project already uses CMake
- generate compile_commands.json
	- add `set(CMAKE_EXPORT_COMPILE_COMMANDS ON)` to your main `CMakeLists.txt`
	- or execute `cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=1 ..`
- 
- then go to [[#Add system header paths]]

Otherwise fake it with the following
### build directory
The build directory should be named "build" and inside the project root directory. Otherwise modify the CMakeLists.txt, .clangd, and shell commands accordingly
### .clangd
Put into project root directory

```
CompileFlags:
  CompilationDatabase: build/

Index:
  Background: Build
  StandardLibrary: Yes

Diagnostics:
  UnusedIncludes: Strict
  MissingIncludes: Strict

Completion:
  AllScopes: Yes
```
### CMakeLists.txt
Put into project root directory

```cmake
cmake_minimum_required(VERSION 3.8)
project(my_project)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Change path from /src if needed, or add more directories
file(GLOB_RECURSE sources
        "${CMAKE_SOURCE_DIR}/*.c"
        #"${CMAKE_SOURCE_DIR}/src/*.c"
        #"${CMAKE_SOURCE_DIR}/src/*.cpp"
        )
# Add precompiler definitions like that:
#add_definitions(-DSOME_DEFINITION)

add_executable(my_app ${sources})

# Add more include directories if needed
#target_include_directories(my_app PUBLIC "${CMAKE_SOURCE_DIR}/include")
target_include_directories(my_app PUBLIC "${CMAKE_SOURCE_DIR}/inc")

# If you have precompiled headers you can add them like this
#target_precompiled_headers(my_app PRIVATE "${CMAKE_SOURCE_DIR}/src/pch.h")
```

#### Rebuild cache
1. Delete the existing clangd cache
```sh
mkdir build

rm -rf build/.cache/clangd 
cmake -S . -G "Unix Makefiles" -B build
```

 2. If you get this error:
```sh
CMake Error: The current CMakeCache.txt directory path1/to/CMakeCache.txt is different than the directory path2/to/build where CMakeCache.txt was created. This may result in binaries being created in the wrong place. If you are not sure, reedit the CMakeCache.txt
CMake Error: The source "path3/to/CMakeLists.txt" does not match the source "path4/to/CMakeLists.txt" used to generate cache.  Re-run cmake with a different source directory.
```

2. Run this to wipe the CMakeCache and then re-run step 1
```sh
find build/ -name "CMakeCache.txt" -exec rm {} \;
```

3. Validate successful  cache with
```sh
clangd --check=ex.c
clangd --check=main.c
```

#### Build
```sh
cd build
cmake .. # point CMake to project root
make
```
## Add system header paths
Add system paths to compile_commands.json
	-[[#add system headers with gcc-ver]]
	- [[#add system headers with enhance_ccjson.py]]

## Give clangd access to compile_commands.json
Finally, move the compile_commands.json file into the project root directory or symlink it
```sh
cd ${project_root_dir}

mv build/compile_commands.json .
#      OR
ln -s build/compile_commands.json .
```
# add system headers with gcc-ver
These paths can be generated with the following `gcc-<ver>` command. Relevant paths are appear in the output in the sections marked
- "#include "..." search starts here" 
- "#include <...> search starts here"
- up to "End of search list."
```sh
$ gcc-12 -E -v -xc++ /dev/null
Using built-in specs.
COLLECT_GCC=gcc-12
...<SNIP>...
#include "..." search starts here:
#include <...> search starts here:
 /usr/include/c++/12
 /usr/include/x86_64-linux-gnu/c++/12
 /usr/include/c++/12/backward
 /usr/lib/gcc/x86_64-linux-gnu/12/include
 /usr/local/include
 /usr/include/x86_64-linux-gnu
 /usr/include
End of search list.
...<SNIP>...
```

# add system headers with enhance_ccjson.py
Put into project root directory

Source: https://dev.to/arunpal_/fixing-clangd-no-locations-found-error-in-neovim-for-c-projects-2pgl
```python
#!/usr/bin/env python3
"""
enhance_compile_commands.py
Enhances compile_commands.json with proper system includes for clangd
"""
import json
import subprocess
import os
import sys

def get_system_includes():
    """Get system include paths from the compiler"""
    try:
        # Get C++ system includes from GCC
        result = subprocess.run(
            ['g++', '-E', '-x', 'c++', '-', '-v'],
            input='',
            capture_output=True,
            text=True
        )

        lines = result.stderr.split('\n')
        includes = []
        in_include_section = False

        for line in lines:
            if '#include <...> search starts here:' in line:
                in_include_section = True
                continue
            elif 'End of search list.' in line:
                in_include_section = False
                continue
            elif in_include_section and line.strip():
                include_path = line.strip()
                if os.path.exists(include_path):
                    includes.append(f'-I{include_path}')

        return includes
    except Exception as e:
        print(f"Error getting system includes: {e}")
        return []

def clean_include_paths(parts):
    """Remove non-existent include paths"""
    cleaned_parts = []
    skip_next = False

    for i, part in enumerate(parts):
        if skip_next:
            skip_next = False
            continue

        if part == '-I' and i + 1 < len(parts):
            # Check if the next part is the include path
            include_path = parts[i + 1]
            if os.path.exists(include_path):
                cleaned_parts.append(part)
                cleaned_parts.append(include_path)
            else:
                print(f"Removing non-existent include path: {include_path}")
            skip_next = True
        elif part.startswith('-I'):
            # Check if the include path exists
            include_path = part[2:]
            if os.path.exists(include_path):
                cleaned_parts.append(part)
            else:
                print(f"Removing non-existent include path: {include_path}")
        else:
            cleaned_parts.append(part)

    return cleaned_parts

def enhance_compile_commands(input_file, output_file=None):
    """Enhance compile_commands.json with system includes"""
    if output_file is None:
        output_file = input_file

    # Read the original compile_commands.json
    with open(input_file, 'r') as f:
        commands = json.load(f)

    # Get system includes
    system_includes = get_system_includes()
    print(f"Found {len(system_includes)} system include paths")

    # Add system includes to each command
    for entry in commands:
        if 'command' in entry:
            # Split the command into parts
            parts = entry['command'].split()

            # Clean up non-existent paths first
            parts = clean_include_paths(parts)

            # Find where to insert the includes (after the compiler command)
            insert_pos = 1
            for i, part in enumerate(parts):
                if part.startswith('-'):
                    insert_pos = i
                    break

            # Insert system includes
            for include in reversed(system_includes):
                if include not in parts:
                    parts.insert(insert_pos, include)

            # Rebuild the command
            entry['command'] = ' '.join(parts)

    # Write the enhanced compile_commands.json
    with open(output_file, 'w') as f:
        json.dump(commands, f, indent=2)

    print(f"Enhanced compile_commands.json written to {output_file}")

if __name__ == "__main__":
    compile_commands_path = "build/compile_commands.json"

    if len(sys.argv) > 1:
        compile_commands_path = sys.argv[1]

    if not os.path.exists(compile_commands_path):
        print(f"Error: {compile_commands_path} not found")
        sys.exit(1)

    enhance_compile_commands(compile_commands_path)

```
