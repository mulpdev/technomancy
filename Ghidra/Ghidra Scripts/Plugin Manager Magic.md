## Related link
https://stackoverflow.com/a/76038373
```
Analyzers aren't Plugins.

Plugins, and the PluginTool they live in, are a GUI concept. Analyzers are decoupled from the GUI and need to work independently of that. (think headless analyzer)

Analyzers can use (`ghidra.util.`)`Msg.info()`, `Msg.warn()`, `Msg.error()` to log messages to ghidra's log. Also, the `Analyzer.added()` method has a `MessageLog` param that you can use to alert the user about issues that during analysis.
```

## Available Plugins and Actions one liners

```python
# Output: ghidra.app.plugin.*.MyNamedPlugin@<addr>
for plugin in [_for _ in state.getTool().getManagedPlugins().iterator()]: print(plugin)

# Output: Do The Thing (PluginName)
for action in [_for _ in state.getTool().getAllActions().iterator()]: print(action)
```

## Helpers
Beginning of function

```python
tool = state.getTool()

def getByName(name, iter_thing, thing):
	# "fuzzy" search
	# things = [_ for _ in tool.getAllActions().iterator() if name in _.name]

	q = [_ for _ in iter_thing if _.name == name]
	if len(q) != 1:
		err = "Found {} {} named {}\n\t{}".format(len(a), thing, name, a)
		raise Exception()
	return actions[0]

def getActionByName(name):
	return getByName(name, tool.getAllActions().iterator(), "Actions")
	
def getPluginByName(name):
	return getByName(name, tool.getManagedPlugins().iterator(), "Plugins")

def doAction(name, context):
	action = getActionByName(name)
	action.actionPerformed(context)
	
def getListingContext():
	return tool.getComponentProvider('Listing').getActionContext(None)
	
def doListingAction(name):
	doAction(name, getListingContext())

```
## Full script definitions
```python
tool = state.getTool()

def getByName(name, iter_thing, thing):
	# "fuzzy" search
	# things = [_ for _ in tool.getAllActions().iterator() if name in _.name]

	q = [_ for _ in iter_thing if _.name == name]
	if len(q) != 1:
		err = "Found {} {} named {}\n\t{}".format(len(a), thing, name, a)
		raise Exception()
	return actions[0]

def getActionByName(name):
	return getByName(name, tool.getAllActions().iterator(), "Actions")
	
def getPluginByName(name):
	return getByName(name, tool.getManagedPlugins().iterator(), "Actions")

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
