# Canonical overflow-measurement script

Single source of truth for the deepest-descendant overflow check. [SKILL.md §Overflow verification workflow](../SKILL.md) and [verification-checklist.md §1](verification-checklist.md) both refer here — if the script ever appears in two places, this file wins.

Open the deck in Chrome via chrome-devtools-mcp, then run the function below verbatim. Expected output: `{ canvas, results, failures, all_ok }`. Delivery requires `all_ok === true`.

```javascript
() => {
  const CANVAS_W = 1707, CANVAS_H = 960;
  const slides = Array.from(document.querySelectorAll('.slide'));
  const results = [];
  for (const slide of slides) {
    const orig = {
      overflow: slide.style.overflow, height: slide.style.height,
      width: slide.style.width, opacity: slide.style.opacity,
      pointerEvents: slide.style.pointerEvents, transition: slide.style.transition,
      animation: slide.style.animation
    };
    try {
      slide.style.transition = 'none';
      slide.style.animation = 'none';
      slide.style.overflow = 'visible';
      slide.style.height = CANVAS_H + 'px';
      slide.style.width = CANVAS_W + 'px';
      slide.style.opacity = '1';
      slide.style.pointerEvents = 'auto';
      void slide.offsetHeight;  // force reflow

      const slideRect = slide.getBoundingClientRect();
      const slideBottom = slideRect.top + CANVAS_H;
      // Find deepest descendant rect (real measure of content extent — works under flex fill)
      let maxBottom = slideRect.top;
      let culprit = null;
      for (const el of slide.querySelectorAll('*')) {
        const cs2 = getComputedStyle(el);
        if (cs2.position === 'absolute' || cs2.position === 'fixed') continue;
        const r = el.getBoundingClientRect();
        if (r.bottom > maxBottom) {
          maxBottom = r.bottom;
          culprit = el.tagName + (el.className ? '.' + String(el.className).split(' ')[0] : '');
        }
      }
      const contentBottom = Math.round(maxBottom - slideRect.top);
      const overshoot = Math.max(0, Math.round(maxBottom - slideBottom));
      const headroom = CANVAS_H - contentBottom;

      results.push({
        id: slide.id,
        contentBottom,
        headroom: Math.round(headroom),
        overshoot,
        culprit,
        ok: overshoot === 0
      });
    } finally {
      Object.assign(slide.style, orig);
    }
  }
  const failures = results.filter(r => !r.ok);
  return { canvas: `${CANVAS_W}×${CANVAS_H}`, results, failures, all_ok: failures.length === 0 };
}
```

## Why each defensive move

**`try / finally`** — a thrown exception (DOM mutation, detached node, etc.) inside the measurement block would leave the slide forced to opaque, full canvas size, visually breaking the deck for the user. `finally` restores the slide style no matter what.

**Filter `position:absolute|fixed` in descendant scan** — cover slides use `#s1::before / ::after` for radial blooms; `.tc` (timecode column) is also absolute. Their `getBoundingClientRect()` reports a real position but they don't block layout. Including them would mark cover slides as overflowing.

**"Deepest descendant" beats "sum of children"** — the vertical-fill contract (`flex: 1 1 auto` on body siblings) means the body container is artificially stretched to fill remaining canvas. Summing the slide's direct children always returns ≈ canvas height. To know if *real content* fits, look at the actual bottom-most rendered rect.

**Force the slide to canvas dimensions before sampling** — the chrome-devtools-mcp browser window is typically 1282×670 (chrome eats space), not the 1707×960 canvas the deck is designed for. `clientHeight` reflects the viewport, not the canvas. Without forcing the size, every measurement lies.
