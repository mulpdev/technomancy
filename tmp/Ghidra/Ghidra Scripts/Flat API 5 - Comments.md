- CodeUnit has the comment types, even if target address not a CodeUnit
- Parameter order is not consistent in these functions
```python
from ghidra.program.model.listing import CodeUnit

PL = currentProgram.getListing()


PL.getComment(CodeUnit.POST_COMMENT, addr)
PL.clearComments(addr_start, addr_end)
PL.setComment(addr, CodeUnit.POST_COMMENT, "This is the comment")
```
