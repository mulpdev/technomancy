https://github.com/GhidraJupyter/ghidra-jupyter-kotlin/issues/19#issuecomment-946632900

```kotlin
import ghidra.program.model.listing.CodeUnit as CodeUnit

var id = currentProgram.startTransaction("Set line comment from Kernel")

var listing = currentProgram.getListing()
listing.setComment(toAddr(0x0), CodeUnit.PLATE_COMMENT, "Hello Ghidra")

currentProgram.endTransaction(id)
```

or even more specific:

```kotlin
import ghidra.program.model.listing.CodeUnit as CodeUnit

var listing = currentProgram.getListing()

var id = currentProgram.startTransaction("Set line comment from Kernel")

listing.setComment(toAddr(0x0), CodeUnit.PLATE_COMMENT, "Hello Ghidra")

currentProgram.endTransaction(id)
```