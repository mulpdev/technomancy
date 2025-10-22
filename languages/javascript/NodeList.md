
# Promise chain
```js
Promise.then(onFulfilled).catch(errorFunc)

// Whatever is return (or resolve()) from promise or func goes into () => and can be used inside anonymous fun
promiseFunc(param1)
  .then( (value) => {...success...})
  .catch( (err) => {...errror...});
```
# Return a value to a promise
returning a param allows it to be passed to next `.then()`
```js
function log_and_pass(prefix, param_1) {
  console.log(prefix + param_1);
  return param_1;
}

function do_main() {
  navigator.clipboard.readText()
    .then(clipboardText => log_and_pass("n.c.readText():", clipboardText))
    .then(clipboardText => clean1(clipboardText))
    .then(cleaned => log_and_pass("cleaned():", cleaned))
    .then(cleaned => navigator.clipboard.writeText(cleaned))
    .then(navigator.clipboard.readText())
    .then(clipboardText => log_and_pass("n.c.readText():", clipboardText))
}

console.log("Hi from extension");
```

# page stuff
From: https://stackoverflow.com/a/17336519

Suppose that you have a page with this address: `http://sub.domain.com/virtualPath/page.htm`.

Use the following in page code to achieve those results:

| Property                   | Result                                                   |
| -------------------------- | -------------------------------------------------------- |
| `window.location.host`     | `sub.domain.com:8080` or `sub.domain.com:80`             |
| `window.location.hostname` | `sub.domain.com`                                         |
| `window.location.protocol` | `http:`                                                  |
| `window.location.port`     | `8080` or `80`                                           |
| `window.location.pathname` | `/virtualPath`                                           |
| `window.location.origin`   | `http://sub.domain.com` (Might include `:port` too*****) |

**Update: about the .origin**

***** As the [ref](https://developer.mozilla.org/en-US/docs/Web/API/window.location) states, browser compatibility for `window.location.origin` is not clear. I've checked it in chrome and it returned `http://sub.domain.com:port` if the port is anything but 80, and `http://sub.domain.com` if the port is 80.

**Special thanks to @torazaburo for mentioning that to me.**

# NodeList object
## Browser Compatability
https://developer.mozilla.org/en-US/docs/Web/API/NodeList#Browser_compatibility
https://caniuse.com/
- ex https://developer.mozilla.org/en-US/docs/Web/API/NodeList/forEach
- ex https://caniuse.com/mdn-api_nodelist_foreach
## Converting NodeList to Array for looping...

```
Modern APIs represent list structures using types based on JavaScript arrays, thus making many array methods available, and at the same time imposing additional semantics on their usage (such as making their items read-only).

...SNIP...

You don't create `NodeList` objects yourself, but you get them from APIs such as Document.querySelectorAll()
```
- Modified from: https://developer.mozilla.org/en-US/docs/Web/API/NodeList 
### via NodeList API 

```js
var div_list = document.querySelectorAll('div'); // returns NodeList
var div_array = [...div_list]; // converts NodeList to Array
div_array.forEach(div => {

// do something awesome with each div

});
```
- Source: https://stackoverflow.com/a/33982079
### via spread operator

`[...List]` - https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator

```js
node_list = document.querySelectorAll('selector');
arr = Array.from(node_list);
arr.forEach( (el) => { 
	console.log('A');
	console.log('B');
});

// One liner
Array.from(document.querySelectorAll('selector')).forEach(el => {
	console.log('A');
	console.log('B');
});
```
Source: https://stackoverflow.com/a/33982079

```js
Array.from(document.querySelectorAll('.s-code-block')).forEach((el) => console.log(el));
```

