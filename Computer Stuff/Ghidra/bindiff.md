zynamics.com/software.html

```
sudo apt instal openjdk-blah

mkdir /opt/gradle
unzip -d /opt/grade/ gradle<whateverver>-bin.zip
export PATH=$PATH:/opt/gradle/gradle-<version.whatever>/bin

mkdir /opt/ghidra
unzip -d /opt/ghidra ghidra_wehatyever.zip
export GHIDRA_INSTALL_DIR=/opt/ghidra-fjserako_PUBLIC/

git clone https://github.com/google/binexport.git
cd binexport/java

gradle

ls dist/
```

# Plugin install
- Must compile plugin for same version of Ghidra
- Extract the bindiff archive OR grab the .zip of the plugin
- File > Extensions
- Hit + icon in top right of Install Extensions window
- select `binexport/java/dist/<zip>`

# Export BinDiff data from Ghidra
- Open binary in Ghidra (Same version plugin was compiled for)
- File > Export Program
- Select "Binary BinExport (v2) for BinDiff"
- Select output filename and click OK
- repeat for comparison binary

# Import into BinDiff
- execute `bindiff -ui`
- Open/Make workspace
- Diffs > New Diff
- PRIMARY: older binary version
- SECONDARY: newer binary version