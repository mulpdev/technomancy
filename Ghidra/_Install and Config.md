
# Install
https://github.com/NationalSecurityAgency/ghidra/releases

Minimum OpenJDK 21
- formerly 17
```sh
sudo apt install openjdk-21-jdk
pacman -Syu jdk21-openjdk
dnf install java-21-openjdk
```

Then extract and add directory to `$PATH`
```sh
# This user only
unzip "${HOME}/Downloads/ghidra_*_PUBLIC_*.zip" -d "${HOME}/.local/bin"

# All Users
sudo unzip $HOME/Downloads/ghidra_*_PUBLIC_*.zip -d /opt 

# Windows
Right Click > 7-Zip > Extract Here

Right Click > Extract All... 
	- Extracts into subdirectory of zipfile name
	- Windows zip is SLOOOOW
```

# Arbitrary Username for Ghidra Database
Edit `{GHIDRA_DIR}/support/launch.properties` and append
```
VMARGS=-Duser.name=username
```
# RAM Usage
Edit `${GHIDRA_DIR}/ghidraRun` 
Uncomment `MAXMEM-2G` and set to something higher like 6G
# HiDPI scaling
Source: https://gist.github.com/nstarke/baa031e0cab64a608c9bd77d73c50fc6

Edit `${GHIDRA_ROOT}/support/launch.properties` and modify this line
```
VMARGS_LINUX=-Dsun.java2d.uiScale=1

VMARGS_LINUX=-Dsun.java2d.uiScale=2
```

# UI Customization
Tools Options > Decompiler > Display
- Globals to Seafoam Green `#008b8b rgb(0,139,139)`
- Comment line Indent level: 10

# Optional pyi for LSPs

Ghidra 11.3+
https://pypi.org/project/ghidra-stubs/
https://pypi.org/project/ghidra-stubs/#files 
- Download `ghidra_stubs-11.4-py3-none-any.whl`
```
The stubs can be installed with `pip install ghidra-stubs*.whl` into the environment in which the real Ghidra module (i.e., `pyghidra`) is available. Any conformant tool will then use the stubs package for type analysis purposes.
```

Ghidra <11.3
https://github.com/VDOO-Connected-Trust/ghidra-pyi-generator/releases/
- the `.whl` file for your version of Ghidra

```bash
mkdir $HOME/ghidra_scripts
cd $HOME/ghidra_scripts
mkdir venv  
python -m venv ./venv
./venv/bin/pip install ghidra-stubs*.whl
```

Then before editing your files
```bash
source ./venv/bin/activate
