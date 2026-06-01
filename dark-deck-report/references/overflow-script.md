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

---

## Typography-floor probe (companion check)

Overflow is necessary but not sufficient: a slide can fit *because* a "fit more text" edit silently dropped a surface below the readable floor (the regression class that the §Typography baselines hard floor exists to stop — body ≥17px, mono labels ≥12.5px, references ≥13px). A static grep cannot catch this — sizes come from CSS vars, inheritance, and per-element overrides, so only the **computed** size is trustworthy. Run this alongside the overflow script.

```javascript
() => {
  // floor → selectors that should never compute below it (adjust to the template's classes)
  const RULES = [
    // body floor — note table td:not(:first-child): the first cell is the mono
    // row-number, governed by the 12.5 mono rule below, so exclude it here.
    { floor: 17,   sel: '.sub, .ch-body, .cq-body, .mpdesc, table td:not(:first-child), .card p, .ib' },
    { floor: 12.5, sel: '.scat, .mptitle, .b, .eyebrow, tbody td:first-child' },
    { floor: 13,   sel: '.rt, .rsrc, .rl2, .rn' },
  ];
  const bad = [];
  for (const { floor, sel } of RULES) {
    document.querySelectorAll(sel).forEach(el => {
      const px = parseFloat(getComputedStyle(el).fontSize);
      if (px + 0.05 < floor) bad.push({ floor, px, sel: el.className || el.tagName,
                                        text: el.textContent.trim().slice(0, 28) });
    });
  }
  return { pass: bad.length === 0, violations: bad };
}
```

`pass: true` is required. Any element below its floor is a **BLOCK** — raise it back to the published baseline and cut content to fit, never compress. (This is the exact probe that caught the brief-v2 regression where `.sub` was 16px and references were 11.5px.)

**v2 carve-out.** In the v2 design system, the micro/marg tier (`--fs-micro` 11px eyebrows incl. `.scat`, `--fs-marg` 10px rotated marginalia, page numbers, fingerprint, figure tags) is an *intentional* exception to the 12.5 mono floor — non-reading chrome, scanned not read (see [design-system-v2.md](design-system-v2.md) FLOOR CARVE-OUT). When probing a v2 deck, drop `.scat` from the 12.5 rule and skip the marginalia classes; the 17/12.5/13 floors still bind every reading surface (body, table cells, references).
