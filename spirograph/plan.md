# Spirograph — Development Plan

## Goal

Build a single, self-contained `spirograph.html` file that lets a user interactively draw spirograph (hypotrochoid / epitrochoid) curves in the browser.

---

## 1. File Structure

Everything lives inside one `spirograph.html` file:

```
<html>
  <head>
    <style>  /* all CSS inline */  </style>
  </head>
  <body>
    <!-- Canvas + control panel -->
    <script> /* all JS inline */ </script>
  </body>
</html>
```

---

## 2. Mathematical Core

### Hypotrochoid equations (inner rolling circle)

```
x(t) = (R - r) · cos(t) + d · cos((R - r) / r · t)
y(t) = (R - r) · sin(t) − d · sin((R - r) / r · t)
```

- **R** — radius of the fixed outer circle
- **r** — radius of the rolling inner circle
- **d** — distance of the pen from the centre of the rolling circle
- **t** — angle parameter (0 → 2π × LCM steps)

The curve closes exactly after `lcm(R, r) / r` full rotations of the inner circle, so the step count is computed from the LCM of R and r.

---

## 3. UI Layout

```
+--------------------------------------------+
|  [ Canvas — fills most of the viewport ]   |
+--------------------------------------------+
|  Control panel (bottom strip or sidebar):  |
|  R slider  |  r slider  |  d slider        |
|  Colour    |  Line width |  Speed           |
|  [Draw]  [Animate]  [Clear]  [Save PNG]    |
+--------------------------------------------+
```

---

## 4. Implementation Steps

### Step 1 — Skeleton HTML
- Create `spirograph.html` with a `<canvas>` element and a `<div id="controls">`.
- Style the page: dark background, canvas centred, controls laid out with flexbox.

### Step 2 — Math helper
- Implement `hypotrochoid(R, r, d, steps)` returning an array of `{x, y}` points.
- Implement `lcm(a, b)` and `gcd(a, b)` helpers to compute the correct number of steps.

### Step 3 — Render function
- `drawCurve(points, colour, lineWidth)` — clears (or layers) the canvas, then strokes the point array.
- Offset the coordinate origin to the canvas centre.

### Step 4 — Controls
- Add `<input type="range">` sliders for R (10–200), r (1–R−1), d (0–200).
- Add `<input type="color">` for pen colour.
- Add a range input for line width (0.5–5 px).
- Wire each slider's `input` event to re-draw immediately (live preview).

### Step 5 — Animation mode
- "Animate" button starts `requestAnimationFrame` loop, drawing one segment per frame.
- A speed slider controls how many segments are drawn per frame.
- "Stop" button cancels the animation.

### Step 6 — Extras
- **Clear** button resets the canvas (with optional fade-out transition).
- **Save PNG** button calls `canvas.toBlob()` and triggers a download.
- **Layer mode** checkbox — skip clearing between draws so curves accumulate.
- Responsive: canvas resizes to the viewport; curve re-centred on resize.

---

## 5. Colour & Style

- Default dark background (`#111`) with a light canvas or transparent canvas over it.
- Gradient stroke option: interpolate hue across the full length of the curve.
- Default palette of preset colour buttons for quick selection.

---

## 6. Edge Cases & Quality

- Clamp `r` so it is always `< R` (hypotrochoid) or enforce `r > R` for epitrochoids (add a mode toggle).
- Guard against `r === 0` (division by zero).
- Cap maximum steps (e.g. 100 000) to keep rendering responsive.
- Handle very small `R − r` values that produce near-degenerate circles.

---

## 7. Deliverable

A single file `spirograph/spirograph.html` that:
- Opens in any modern browser with no build step or network requests.
- Draws correct spirograph curves for any valid R / r / d combination.
- Provides an intuitive control panel for interactive exploration.
- Allows the user to save their artwork as a PNG.
