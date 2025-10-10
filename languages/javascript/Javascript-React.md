# Find root elements for React using ECMA
Source: https://pablo.gg/en/blog/coding/how-to-get-the-redux-state-from-a-react-18-production-build-via-the-browsers-console/
```js
// Find the root elements for React
const internalRoots = Array.from(document.querySelectorAll("*[id]")).map((el) => {
    const key = Object.keys(el).filter((keyName) => keyName.includes('__reactContainer')).at(-1);

    return el[key];
}).filter((key) => key);

const stores = new Set();
// code from https://gist.github.com/mindplay-dk/1843c267fc633688059dfa5e3b07d0dd
internalRoots.forEach((root) => {
    let seen = new Map();
    function search(obj) {
        if (seen.has(obj)) {
            return;
        }

        seen.set(obj, true);
        for (name in obj) {
            if (name === 'getState') {
                stores.add(obj);
            }

            if ((obj?.hasOwnProperty?.(name)) && (typeof obj[name] === "object") && (obj[name] != null)) {
                search(obj[name]);
            }
        }
    }

    search(root);
});
```

# clicking
Simple ECMA click event
```js
ele.click()
```

MouseEvent takes an optional second parameter "options" (the `kv` object in snippets). 
- https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent/MouseEvent#options
	- base options to MouseEvent: 
		- `button`  (singular) which button is pressed during events **related to the press or release** of a button
			- 0: Main (left?) button pressed
			- 1: Aux (mid?)button pressed
			- 2: Secondary (right?) button pressed
		- `buttons` (plural) which buttons are pressed when the event is launched (BitField)
			- 0: No button press
			- 1: Main (left?) button pressed
			- 2: Aux (mid?)button pressed
			- 4: Secondary (right?) button pressed
- https://developer.mozilla.org/en-US/docs/Web/API/UIEvent/UIEvent#parameters
- https://developer.mozilla.org/en-US/docs/Web/API/UIEvent/initUIEvent#parameters
- https://dom.spec.whatwg.org/#dom-event-bubbles
	- more options can be defined in `UIEvent`
		- The `bubbles` and `cancelable` attributes must return the values they were initialized to.
			- `bubbles` should bubble up through Event chain?
		- `view` The `Window` object associated with event. Default is null


Before React 16
```js
ele.dispatchEvent(new MouseEvent('click', {view: window, bubbles: true, cancelable: true, buttons: 1}));
```

React current
Derived from: 
- https://stackoverflow.com/a/54316368 for mouseevents vs click
```js
kv = {view: window, bubbles: true, cancelable: true, buttons: 1};
reactButton.dispatchEvent(new MouseEvent('mousedown', kv));
//await new Promise(r => setTimeout(r, 50));
reactButton.dispatchEvent(new MouseEvent('click', kv));
//await new Promise(r => setTimeout(r, 50));
reactButton.dispatchEvent(new MouseEvent('mouseup', kv));
//await new Promise(r => setTimeout(r, 50));
```

- https://stackoverflow.com/a/63375334 "realism" for delay, needs all 3 down/click/up events
```js
kv = {view: window, bubbles: true, cancelable: true, buttons: 1};
reactButton.dispatchEvent(new MouseEvent('mousedown', kv));
await new Promise(r => setTimeout(r, 50));
reactButton.dispatchEvent(new MouseEvent('click', kv));
reactButton.dispatchEvent(new MouseEvent('mouseup', kv));
```

## click React Button
```js
async function clickReactButton(reactButton) {
	kv = {view: window, bubbles: true, cancelable: true, buttons: 1};
	reactButton.dispatchEvent(new MouseEvent('mousedown', kv));
	await new Promise(r => setTimeout(r, 50));
	reactButton.dispatchEvent(new MouseEvent('click', kv));
	await new Promise(r => setTimeout(r, 50));
	reactButton.dispatchEvent(new MouseEvent('mouseup', kv));
	await new Promise(r => setTimeout(r, 50));
}
```

## click React Select dropdown
```js
function clickReactSelectDropownThing(reactEle) {
	kv = {bubbles: true};
	reactEle.dispatchEvent(new MouseEvent('mousedown', kv));
}
```

## setting Values
```js
ele.value = "newValue"; // always in quotes
```

```js
ele.value = "newValue";
ele._valueTracker.setValue("old"); // Anything that isn't current value?

// React 15 (or all? simulated prevents ignoring events)
ele.simulated = True;

// React 16
ele.dispatch(new Event("input", {target: ele, bubbles: true}));
```