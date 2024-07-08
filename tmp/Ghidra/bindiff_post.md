# Downloads / Requirements
## JDK
As of 10.4 minimum java version is 17
```
sudo apt install openjdk-17-jdk
```

## Ghidra
https://github.com/NationalSecurityAgency/ghidra/releases

## BinExport
https://github.com/google/binexport
release or git clone

## Gradle
https://gradle.org/releases/

The document at https://github.com/google/binexport/tree/main/java says 5.6+ but execution says minimum is 7.3 

```
user@shine:~/Downloads/ghibin$ python3 make_binexport.py --ghidra ghidra/ghidra_10.3_PUBLIC/ --gradle gradle/gradle-5.6.3-all/gradle-5.6.3/ --binexport binexport/binexport-12/                            
Welcome to Gradle 5.6.3!                                     

Here are the highlights of this release:                      - Incremental Groovy compilation                             - Groovy compile avoidance                                   - Test fixtures for Java projects                            - Manage plugin versions via settings script                For more details see https://docs.gradle.org/5.6.3/release-notes.html

Starting a Gradle Daemon (subsequent builds will be faster)

FAILURE: Build failed with an exception.                   

* Where:                                                     Script '/home/user/Downloads/ghibin/ghidra/ghidra_10.3_PUBLIC/support/buildExtension.gradle' line: 360                         

* What went wrong:                                           A problem occurred evaluating script.                        > Requires at least Gradle 7.3, but was run with 5.6.3       ...SNIP...                                   
```

8.4 gives this:
```
python3 make_binexport.py --ghidra ghidra/ghidra_10.3_PUBLIC/ --gradle gradle/gradle-8.4/ --binexport binexport/binexport-12/
Starting a Gradle Daemon, 2 incompatible and 1 stopped Daemons could not be reused, use --status for details
> Task :extractIncludeProto FAILED

FAILURE: Build failed with an exception.

* What went wrong:
A problem was found with the configuration of task ':extractIncludeProto' (type 'ProtobufExtract').
  - Gradle detected a problem with the following location: '/home/user/Downloads/ghibin/binexport/binexport-12/java/lib/protobuf-java-3.13.0.jar'.
    
    Reason: Task ':extractIncludeProto' uses this output of task ':copyDependencies' without declaring an explicit or implicit dependency. This can lead to incorrect results being produced, depending on what order the tasks are executed.
    
    Possible solutions:
      1. Declare task ':copyDependencies' as an input of ':extractIncludeProto'.
      2. Declare an explicit dependency on ':copyDependencies' from ':extractIncludeProto' using Task#dependsOn.
      3. Declare an explicit dependency on ':copyDependencies' from ':extractIncludeProto' using Task#mustRunAfter.
    
    For more information, please refer to https://docs.gradle.org/8.4/userguide/validation_problems.html#implicit_dependency in the Gradle documentation.
...SNIP...
```
 
 7.6.4-all worked just fine though

```
# Bindiff 7, Ghidra 10.3
python3 make_binexport.py --ghidra ghidra/ghidra_10.3_PUBLIC/ --gradle gradle/gradle-7.6.3-all/gradle-7.6.3/ --binexport binexport/binexport-12-20230515-ghidra_10.3

# Bindiff 7, Ghidra 10.4
python3 make_binexport.py --ghidra ghidra/ghidra_10.4_PUBLIC/ --gradle gradle/gradle-7.6.3-all/gradle-7.6.3/ --binexport binexport/binexport-12-20230515-ghidra_10.3

# Bindiff 8, Ghidra 10.3
python3 make_binexport.py --ghidra ghidra/ghidra_10.4_PUBLIC --gradle gradle/gradle-7.6.3-all/gradle-7.6.3/ --binexport binexport/binexport-12-20230925-bindiff8

# Bindiff 8, Ghidra 10.4
python3 make_binexport.py --ghidra ghidra/ghidra_10.4_PUBLIC --gradle gradle/gradle-7.6.3-all/gradle-7.6.3/ --binexport binexport/binexport-12-20230925-bindiff8
```

# Python
```python
import argparse
import os

def parseArgs():
    usage = ""
    parser = argparse.ArgumentParser(allow_abbrev=False, formatter_class=argparse.ArgumentDefaultsHelpFormatter, epilog=usage)
    parser.add_argument("--ghidra", help="path to ghidra install", type=str, required=True)
    parser.add_argument("--gradle", help="path to gradle install", type=str, required=True)
    parser.add_argument("--binexport", help="path to binexport source", type=str, required=True)
    return parser.parse_args()


def main():
    args = parseArgs()

    gradle_path = os.path.abspath(args.gradle)
    ghidra_path = os.path.abspath(args.ghidra)


    #os.chdir(f'{args.gradle}')
    #os.system(f'PATH=$PATH:{args.gradle}/bin 

    os.chdir(f'{args.binexport}/java')

    cmd = f'GHIDRA_INSTALL_DIR={ghidra_path} {gradle_path}/bin/gradle'
    os.system(cmd)

if __name__ == "__main__":
    main()
```