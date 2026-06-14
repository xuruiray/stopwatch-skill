# HTML Prototype Parity

Use this reference when creating or updating `web/index.html` for a StopWatch app repo.

## Purpose

The HTML page is not a marketing site. It is a browser-runnable interaction prototype that helps tune layout, animation, timing, colors, and state transitions before or alongside LVGL implementation.

## Required Shape

- Single `web/index.html`.
- Vanilla HTML/CSS/JS unless the user asks otherwise.
- Works through `file://`.
- Uses a 466x466 circular device viewport.
- Simulates hardware left/right keys with on-screen buttons and keyboard keys.
- Avoids explanatory in-app text that will not exist on device.

## Parity Checklist

Mirror these from hardware when HTML is the spec:

- State machine names and transition rules.
- Input semantics for left/right/both buttons.
- Timing constants such as debounce, animation, feedback interval, inertia, and result formatting.
- Geometry constants such as center, radius, ring widths, tooth count, or touch hit areas.
- Randomization rules and fixed color palettes when they affect task difficulty.
- Feedback rules: correct/miss sounds, vibration duration, minimum interval, and whether feedback queues.

## Browser Validation

Use Playwright or the in-app browser to verify:

- Page loads without console errors.
- Main viewport/canvas is nonblank.
- Pointer input changes visible state.
- Keyboard/buttons map to expected actions.
- Text fits in the circular viewport.

Example Playwright checks:

```js
await page.goto("file:///absolute/path/web/index.html");
const errors = [];
page.on("pageerror", e => errors.push(e.message));
page.on("console", m => { if (m.type() === "error") errors.push(m.text()); });
```

## Migration Notes

- Do not migrate browser-only explanatory panels into hardware UI.
- Replace DOM nodes with LVGL containers/labels/canvas/draw events.
- Replace browser audio/vibration helpers with `GetHAL().audioPlay()` and `GetHAL().vibrate()`.
- Keep the HTML prototype after migration as a regression reference.
