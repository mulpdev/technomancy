# Name/Version
ECMA Script
Version 6 renamed to "ECMAScript 2015" released 06/2015
Version 7+ are published annually (usually June) and named "ECMAScript {year}"

# Template Strings
ES6+
Enclose string in BACKTICKS, not quotes,
Enclose variables like POSIX shell, `${varname}`
```js
// Template strings, enclose in backticks
let prefix = "MEME1"
let oldValue = "Broke"
let newValue = "Woke"
console.log(`${prefix} Old: ${oldValue} New: ${netValue}`);
```

# Default Params

```js
function doThing(a, b = 1) {
}

// OLD WAY
function doThingOld(a,b) {
	b = typeof b !== "undefinied" ? b : 1 //OLD WAY
}
```

# clicking

```js
ele.click()
```

# sleep_function_hack
Source: https://stackoverflow.com/a/63375334
in an `async` function a sleep can be faked
```js
let timeinmilli=50
await new Promise(r => setTimeout(r, timeinmilli));
```