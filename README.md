# React onPointerDown + iOS bug

## Reproduction steps

1. Run `npm run dev`
2. Open in iOS browser
3. Click on the button
4. Notice that event handler is not called
5. Remove `display: contents` and reload
6. Notice that event handler is now called.

## Probable cause

React adds an empty event handler for click events on every node with an onClick prop. 
Adding a fake pointerDown event listener in the same function fixes the issue,
making display: contents no longer problematic.

```js
function trapClickOnNonInteractiveElement(node) {
    // Mobile Safari does not fire properly bubble click events on
    // non-interactive elements, which means delegated click listeners do not
    // fire. The workaround for this bug involves attaching an empty click
    // listener on the target node.
    // https://www.quirksmode.org/blog/archives/2010/09/click_event_del.html
    // Just set it using the onclick property so that we don't have to manage any
    // bookkeeping for it. Not sure if we need to clear it when the listener is
    // removed.
    // TODO: Only do this for the relevant Safaris maybe?
    node.onclick = noop;
    
    // The fix
    node.onpointerdown = noop;
  }
```
