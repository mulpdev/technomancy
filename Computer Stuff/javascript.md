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