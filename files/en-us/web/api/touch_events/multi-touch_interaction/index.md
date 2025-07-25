---
title: Multi-touch interaction
slug: Web/API/Touch_events/Multi-touch_interaction
page-type: guide
---

{{DefaultAPISidebar("Touch Events")}}

The touch event interfaces support application-specific single and multi-touch interactions. However, the interfaces can be a bit tricky for programmers to use because touch events are very different from other DOM input events, such as [mouse events](/en-US/docs/Web/API/MouseEvent). The application described in this guide shows how to use touch events for simple single and multi-touch interactions, the basics needed to build application-specific gestures.

A _live_ version of this application is available on [GitHub](https://mdn.github.io/dom-examples/touchevents/Multi-touch_interaction.html). The [source code is available on GitHub](https://github.com/mdn/dom-examples/tree/main/touchevents) and pull requests and [bug reports](https://github.com/mdn/dom-examples/issues) are welcome.

## Example

This example demonstrates using the {{domxref("Element/touchstart_event", "touchstart")}}, {{domxref("Element/touchmove_event", "touchmove")}}, {{domxref("Element/touchcancel_event", "touchcancel")}}, and {{domxref("Element/touchend_event", "touchend")}}) touch events for the following gestures: single touch, two (simultaneous) touches, more than two simultaneous touches, 1-finger swipe, and 2-finger move/pinch/swipe.

### Define touch targets

The application uses {{HTMLElement("div")}} elements to represent four touch areas.

```css
div {
  margin: 0em;
  padding: 2em;
}
#target1 {
  background: white;
  border: 1px solid black;
}
#target2 {
  background: white;
  border: 1px solid black;
}
#target3 {
  background: white;
  border: 1px solid black;
}
#target4 {
  background: white;
  border: 1px solid black;
}
```

### Global state

`tpCache` is used to cache touch points for processing outside of the event where they were fired.

```js
// Log events flag
let logEvents = false;

// Touch Point cache
const tpCache = [];
```

### Register event handlers

Event handlers are registered for all four touch event types. The {{domxref("Element/touchend_event", "touchend")}} and {{domxref("Element/touchcancel_event", "touchcancel")}} event types use the same handler.

```js
function setHandlers(name) {
  // Install event handlers for the given element
  const el = document.getElementById(name);
  el.addEventListener("touchstart", startHandler);
  el.addEventListener("touchmove", moveHandler);
  // Use same handler for touchcancel and touchend
  el.addEventListener("touchcancel", endHandler);
  el.addEventListener("touchend", endHandler);
}

function init() {
  setHandlers("target1");
  setHandlers("target2");
  setHandlers("target3");
  setHandlers("target4");
}
```

### Move/Pinch/Zoom handler

This function provides very basic support for 2-touch horizontal move/pinch/zoom handling. The code does not include error handling, or vertical moving. Note that the _threshold_ for pinch and zoom movement detection is application specific (and device dependent).

```js
// This is a very basic 2-touch move/pinch/zoom handler that does not include
// error handling, only handles horizontal moves, etc.
function handlePinchZoom(ev) {
  if (ev.targetTouches.length === 2 && ev.changedTouches.length === 2) {
    // Check if the two target touches are the same ones that started
    // the 2-touch
    const point1 = tpCache.findLastIndex(
      (tp) => tp.identifier === ev.targetTouches[0].identifier,
    );
    const point2 = tpCache.findLastIndex(
      (tp) => tp.identifier === ev.targetTouches[1].identifier,
    );

    if (point1 >= 0 && point2 >= 0) {
      // Calculate the difference between the start and move coordinates
      const diff1 = Math.abs(
        tpCache[point1].clientX - ev.targetTouches[0].clientX,
      );
      const diff2 = Math.abs(
        tpCache[point2].clientX - ev.targetTouches[1].clientX,
      );

      // This threshold is device dependent as well as application specific
      const PINCH_THRESHOLD = ev.target.clientWidth / 10;
      if (diff1 >= PINCH_THRESHOLD && diff2 >= PINCH_THRESHOLD)
        ev.target.style.background = "green";
    } else {
      // empty tpCache
      tpCache.length = 0;
    }
  }
}
```

### Touch start handler

The {{domxref("Element/touchstart_event", "touchstart")}} event handler caches touch points to support 2-touch gestures. It also calls {{domxref("Event.preventDefault","preventDefault()")}} to keep the browser from applying further event handling (for example, mouse event emulation).

```js
function startHandler(ev) {
  // If the user makes simultaneous touches, the browser will fire a
  // separate touchstart event for each touch point. Thus if there are
  // three simultaneous touches, the first touchstart event will have
  // targetTouches length of one, the second event will have a length
  // of two, and so on.
  ev.preventDefault();
  // Cache the touch points for later processing of 2-touch pinch/zoom
  if (ev.targetTouches.length === 2) {
    for (const touch of ev.targetTouches) {
      tpCache.push(touch);
    }
  }
  if (logEvents) log("touchStart", ev, true);
  updateBackground(ev);
}
```

### Touch move handler

The {{domxref("Element/touchmove_event", "touchmove")}} handler calls {{domxref("Event.preventDefault","preventDefault()")}} for the same reason mentioned above, and invokes the pinch/zoom handler.

```js
function moveHandler(ev) {
  // Note: if the user makes more than one "simultaneous" touches, most browsers
  // fire at least one touchmove event and some will fire several touch moves.
  // Consequently, an application might want to "ignore" some touch moves.
  //
  // This function sets the target element's border to "dashed" to visually
  // indicate the target received a move event.
  //
  ev.preventDefault();
  if (logEvents) log("touchMove", ev, false);
  // To avoid too much color flashing many touchmove events are started,
  // don't update the background if two touch points are active
  if (!(ev.touches.length === 2 && ev.targetTouches.length === 2))
    updateBackground(ev);

  // Set the target element's border to dashed to give a clear visual
  // indication the element received a move event.
  ev.target.style.border = "dashed";

  // Check this event for 2-touch Move/Pinch/Zoom gesture
  handlePinchZoom(ev);
}
```

### Touch end handler

The {{domxref("Element/touchend_event", "touchend")}} handler restores the event target's background color back to its original color.

```js
function endHandler(ev) {
  ev.preventDefault();
  if (logEvents) log(ev.type, ev, false);
  if (ev.targetTouches.length === 0) {
    // Restore background and border to original values
    ev.target.style.background = "white";
    ev.target.style.border = "1px solid black";
  }
}
```

### Application UI

The application uses {{HTMLElement("div")}} elements for the touch areas and provides buttons to enable logging and clear the log.

```html
<div id="target1">Tap, Hold or Swipe me 1</div>
<div id="target2">Tap, Hold or Swipe me 2</div>
<div id="target3">Tap, Hold or Swipe me 3</div>
<div id="target4">Tap, Hold or Swipe me 4</div>

<!-- UI for logging/debugging -->
<button id="toggle-log">Start/Stop event logging</button>
<button id="clear-log">Clear the log</button>
<output id="output"></output>
```

### Miscellaneous functions

These functions support the application but aren't directly involved with the event flow.

#### Update background color

The background color of the touch areas will change as follows: no touch is `white`; one touch is `yellow`; two simultaneous touches is `pink`, and three or more simultaneous touches is `lightblue`. See [touch move handler](#touch_move_handler) for information about the background color changing when a 2-finger move/pinch/zoom is detected.

```js
function updateBackground(ev) {
  // Change background color based on the number simultaneous touches
  // in the event's targetTouches list:
  //   yellow - one tap (or hold)
  //   pink - two taps
  //   lightblue - more than two taps
  switch (ev.targetTouches.length) {
    case 1:
      // Single tap`
      ev.target.style.background = "yellow";
      break;
    case 2:
      // Two simultaneous touches
      ev.target.style.background = "pink";
      break;
    default:
      // More than two simultaneous touches
      ev.target.style.background = "lightblue";
  }
}
```

#### Event logging

The functions are used to log event activity to the application window, to support debugging and learning about the event flow.

```js
const output = document.getElementById("output");

function toggleLog(ev) {
  logEvents = !logEvents;
}

document.getElementById("toggle-log").addEventListener("click", toggleLog);

function log(name, ev, printTargetIds) {
  let s =
    `${name}: touches = ${ev.touches.length} ; ` +
    `targetTouches = ${ev.targetTouches.length} ; ` +
    `changedTouches = ${ev.changedTouches.length}`;
  output.innerText += `${s}\n`;

  if (printTargetIds) {
    s = "";
    for (const touch of ev.targetTouches) {
      s += `... id = ${touch.identifier}\n`;
    }
    output.innerText += s;
  }
}

function clearLog(event) {
  output.textContent = "";
}

document.getElementById("clear-log").addEventListener("click", clearLog);
```

## See also

- [Pointer events](/en-US/docs/Web/API/Pointer_events)
