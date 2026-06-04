# Fix: Apple Pencil strokes get cancelled mid-draw

Paste this into a Claude session (or hand to a developer) to patch any lecture slide deck or drawing tool that uses the same canvas pattern.

## The problem

On iPad with Apple Pencil, drawing strokes get terminated before the user lifts the Pencil. Symptoms:

- A continuous Pencil stroke stops on its own and the user has to start over
- Resting a palm on the screen while drawing immediately kills the in-progress stroke
- Strokes terminate when the Pencil tip drifts to the edge of the canvas

Root cause: three issues in the canvas drawing code.

1. `pointerleave` is registered as a stroke-end event. With `setPointerCapture` set on pointerdown, it usually does not fire mid-stroke, but on several iPadOS Safari builds it fires when the Pencil tip approaches the canvas edge, ending the stroke prematurely.
2. The end-stroke handler does not check pointer ID. Any pointer event ending (a palm touch lifting, a second finger releasing) cancels the active Pencil stroke even when the Pencil is still down.
3. The canvas `touchstart` handler calls `preventDefault()` when a non-stylus touch lands. On some iPad models, `touch.touchType === 'stylus'` returns the wrong value for Apple Pencil, which causes preventDefault to fire while the Pencil is drawing. That can trigger a `pointercancel` for the Pencil pointer and end the stroke.

## The fix

Find the canvas pointer/touch event registration block. In a typical drawing tool it looks something like this:

```js
function start(ev) {
  ev.preventDefault();
  if (canvas.setPointerCapture) canvas.setPointerCapture(ev.pointerId);
  var p = getXY(ev);
  state.currentStroke = { color: state.color, size: state.size, ... , points: [p] };
  state.strokes.push(state.currentStroke);
  drawStrokeSegment(state, state.currentStroke, 0);
}

function end() {
  if (!state.currentStroke) return;
  state.currentStroke = null;
}

canvas.addEventListener('pointerdown', start);
canvas.addEventListener('pointermove', move);
canvas.addEventListener('pointerup',    end);
canvas.addEventListener('pointercancel', end);
canvas.addEventListener('pointerleave', end);

canvas.addEventListener('touchstart', function (e) {
  var t = e.touches && e.touches[0];
  if (t && t.touchType === 'stylus') return;
  e.preventDefault();
}, { passive: false });

canvas.addEventListener('touchmove', function (e) {
  var t = e.touches && e.touches[0];
  if (t && t.touchType === 'stylus') return;
  e.preventDefault();
}, { passive: false });
```

Apply these three changes.

### Change 1: track the pointer ID and type when a stroke starts

In the `start` function, after pushing the new stroke onto `state.strokes`, add two lines that record the pointer ID and pointer type. This lets the rest of the code identify which pointer owns the active stroke.

```js
function start(ev) {
  ev.preventDefault();
  if (canvas.setPointerCapture) {
    try { canvas.setPointerCapture(ev.pointerId); } catch (e) {}
  }
  var p = getXY(ev);
  state.currentStroke = { color: state.color, size: state.size, ... , points: [p] };
  state.currentPointerId   = ev.pointerId;       // NEW
  state.currentPointerType = ev.pointerType;     // NEW ("pen", "touch", "mouse")
  state.strokes.push(state.currentStroke);
  drawStrokeSegment(state, state.currentStroke, 0);
}
```

Also wrap `setPointerCapture` in try/catch as shown. It throws in rare cases on iPad and the throw would prevent the stroke from being recorded.

### Change 2: end the stroke only if the same pointer is lifting, and remove pointerleave

Replace the `end` function and the listener list so that:

- `end` accepts the event and checks that the pointer ID matches the active stroke
- `pointerleave` is no longer registered

```js
function end(ev) {
  if (!state.currentStroke) return;
  if (ev && ev.pointerId != null
      && state.currentPointerId != null
      && ev.pointerId !== state.currentPointerId) return;
  state.currentStroke      = null;
  state.currentPointerId   = null;
  state.currentPointerType = null;
}

canvas.addEventListener('pointerdown', start);
canvas.addEventListener('pointermove', move);
canvas.addEventListener('pointerup',    end);
canvas.addEventListener('pointercancel', end);
// pointerleave intentionally NOT registered. It can fire when the Pencil tip
// drifts to the edge of the canvas mid-stroke on some iPadOS builds, even with
// pointer capture set. pointerup and pointercancel cover legitimate lift cases.
```

### Change 3: protect the active Pencil stroke from the touch handler

In `touchstart` and `touchmove`, before calling `preventDefault`, check whether a pen stroke is currently in progress. If yes, the second touch is almost certainly a palm rest, not a real second-finger gesture. Skip the preventDefault so the system does not fire `pointercancel` for the Pencil.

```js
canvas.addEventListener('touchstart', function (e) {
  var t = e.touches && e.touches[0];
  if (t && t.touchType === 'stylus') return;
  // Palm-rest guard: if a pen stroke is already in progress, leave it alone.
  if (state.currentStroke && state.currentPointerType === 'pen') return;
  e.preventDefault();
}, { passive: false });

canvas.addEventListener('touchmove', function (e) {
  var t = e.touches && e.touches[0];
  if (t && t.touchType === 'stylus') return;
  if (state.currentStroke && state.currentPointerType === 'pen') return;
  e.preventDefault();
}, { passive: false });
```

## Verifying the fix on iPad

After applying the three changes, test on iPad with Apple Pencil. Apple Pencil should pass each of these without dropping the stroke:

1. Draw a long, continuous line across the canvas. The stroke should not break.
2. Draw with palm resting on the screen. The stroke should not break.
3. Draw to the edge of the canvas and back. The stroke should not break.
4. Draw, then lift the Pencil cleanly. The stroke should end exactly when the Pencil lifts.

If strokes still break, the next thing to check is whether the iPad has Apple Pencil Scribble turned on. Scribble intercepts Pencil events for handwriting input fields and can interfere with drawing canvases. Settings > Apple Pencil > Scribble: off.

## Notes for the implementer

These three changes are minimal and surgical. They do not change the public behavior of the drawing tool. Existing strokes draw the same, color and size controls behave the same, and undo/clear/save logic does not need to change.

If the tool also has a multi-touch pinch-to-zoom handler attached to a parent element, apply the same `state.currentStroke && state.currentPointerType === 'pen'` guard to that handler too, so that palm + Pencil never triggers pinch.

If the tool has any CSS rule like `.canvas-wrap { touch-action: none; }` on the *parent* of the canvas, change it so `touch-action: none` lives only on the `.draw-canvas` element itself. The parent rule suppresses Apple Pencil pointer events to descendants on some iPadOS Safari builds.

By Dr. Sharilyn Rennie
