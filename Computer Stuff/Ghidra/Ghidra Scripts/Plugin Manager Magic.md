## Available Actions
Actions are found via something like
```python
print([_for _ in state.getTool().getAllActions().iterator() if _.name == name])
```

```python

filename = ''
with open(filename, 'w') as fd:
	actions = [_for _ in state.getTool().getAllActions().iterator()]
	for action in actions:
		fd.write("{}\n".format(action.name))
```
## Full script definitions
```python
tool = state.getTool()

def getActionByName(name):
	actions = [_ for _ in tool.getAllActions().iterator() if _.name == name]
	if len(actions) != 1:
		raise Exception("Found {} actions named {}".format(len(actions), name))
	return actions[0]
	
def getPluginByName(name):
	plugins = [_ for _ in tool.getManagedPlugins().iterator() if _.name == name]
	if len(plugins) != 1:
		raise Exception("Found {} plugins named {}".format(len(plugins), name))
	return plugins[0]

def getListingContext():
	return tool.getComponentProvider('Listing').getActionContext(None)

def doAction(name, context):
	action = getActionByName(name)
	action.actionPerformed(context)

def doListingAction(name):
	doAction(name, getListingContext())

def nextFunction():
	doAction('Go To Next Function')

def recreateFunc():
	from ghidra.app.cmd.function import CreationFunctionCmd
	from ghidra.program.model.symbol import SourceType
	listingContext = getListingContext()
	recreateFuncAction = getActionByName('Re-create Function')
	funcPlugin = getPluginByName('FunctionPlugin').tool
	entry = listingContext.getAddress()
	name = None
	body = None
	allowExisting = True
	cmd = CreateFunctionCmd(name, entry, body, SourceType.USER_DEFINED, allowExisting, allowExisting)
	funcPlugin.execute(cmd, listingContext.getProgram())

for func in currentProgram.getFunctionManager().getFunctions(currentAddress, True):
	recreateFunc()
```

```python
def getAction(name):
   return [_ for _ in tool.getAllActions().iterator() if _.name == name][0]
def getDecompContext():
   return tool.getComponentProvider('Decompiler').getActionContext(None)
def doDecompAction(name):
  context = getDecompContext()
  action = getAction(name)
  action.actionPerformed(context)
doAction('Commit Params/Return')
```
## Open another ProgramDB in script
-  e.g., for diffing purposes
```python
tool = state.getTool()
def getPluginByName(name):
	plugins = [_ for _ in tool.getAllActions().iterator() if _.name == name]
	if len(plugins) != 1:
		raise Exception('Found {} plugins named {}".format(len(plugins), name))
	return plugins[0]

plugin = getPluginByName('ProgramManagerPlugin')
progPath = '/path/to/prog'
plugin.openProgram(state.getProject().getProjectData().getFile(progPath), -1)
```

## Generic Function ??
- Not sure if there is another context besides GUI actions where this paradigm makes sense
```python
tool = state.getTool()
def getTHINGByName(name):
	THINGS = [_ for _ in tool.getTHINGTYPE().iterator() if _.name == name]
	if len(THINGS) != 1:
		raise Exception('Found {} THINGS named {}".format(len(THINGS), name))
	return THINGS[0]
```
