https://www.reddit.com/r/ghidra/comments/1205pnt/python_api_apply_function_prototype/

```python
from ghidra.app.cmd.function import ApplyFunctionSignatureCmd
from ghidra.program.model.symbol import SourceType

func = createFunction(address, "function_name")
sig = getDataTypes("signature_name")[0]
cmd = ApplyFunctionSignatureCmd(func.getEntryPoint(), sig,SourceType.USER_DEFINED)                                                                                                          
runCommand(cmd)
```

