# Verification — runnable checks before delivery

Three checks turn the SKILL's `MUST` rules from prose into something falsifiable. Run all three after writing the HTML and before telling the user it's done. Each maps to a Non-Negotiable Rule.

> These are **postcondition** checks (Design-by-Contract): a failure means *this skill's own output* is wrong → **BLOCK** and fix; do not deliver behind a warning.

---

## Quick verify — two pastes total

Don't run the three blocks below one at a time. Use these two:

**1) Tokens (shell, one line):**
```bash
grep -nE "\{\{|LABEL_[A-Z]" output/poster-*.html   # expect: no output
```

**2) Geometry + typography (one `browser_evaluate`, after serving + `browser_resize` to the canvas):**
```js
() => {
  const poster = document.querySelector('.poster');
  const cb = poster.getBoundingClientRect().bottom;
  let maxBottom = 0, worst = null;
  poster.querySelectorAll('*').forEach(el => {
    const cs = getComputedStyle(el);
    if (cs.position === 'absolute' || cs.position === 'fixed') return;
    const b = el.getBoundingClientRect().bottom;
    if (b > maxBottom) { maxBottom = b; worst = el; }
  });
  const overshoot = Math.round(maxBottom - cb);
  const FLOOR = window.innerWidth > window.innerHeight ? 22 : 26;       // 26 portrait / 22 landscape
  const bad = [];
  ['.content-card p', '.hero-sub'].forEach(sel =>                       // body copy ONLY
    document.querySelectorAll(sel).forEach(el => {
      const px = parseFloat(getComputedStyle(el).fontSize);
      if (px + 0.05 < FLOOR) bad.push({ sel, px, text: el.textContent.trim().slice(0, 30) });
    }));
  return {
    overflow: { overshoot, all_ok: overshoot <= 0, worst: worst && (worst.className || worst.tagName) },
    typography: { floor: FLOOR, pass: bad.length === 0, violations: bad },
    DONE: overshoot <= 0 && bad.length === 0   // ← deliver only when true (and step 1 is clean)
  };
}
```

Deliver only when step 1 prints nothing **and** `DONE: true`. The sections below explain each check and what to do on failure.

---

## 1. Placeholder / label scan (cheap, static — run first)

Catches the #1 build defect: an unfilled `{{TOKEN}}` or an SVG illustration left with `LABEL_*` stubs. Maps to Output-checklist boxes 1–2 and the "Scan before writing" rule.

```bash
grep -nE "\{\{|LABEL_[A-Z]" output/poster-*.html
```

Any hit is a **BLOCK**. Expected output: nothing (exit 1 / no lines). If a hit appears, fill it from the input or delete the optional section — never ship the token.

---

## 2. Overflow check (runtime — `overflow:hidden` lies, so measure geometry)

The canvas is fixed and `overflow:hidden`, so `scrollHeight − clientHeight` returns 0 even when content is clipped (same gotcha as dark-deck-report). Measure the **deepest descendant's bottom** against the canvas bottom instead.

Serve the file (Playwright blocks `file://`), then run the probe in the page:

```bash
# from ./output/ — use the project python, not the Windows built-in
C:\Programs\miniforge3\envs\deve\python.exe -m http.server 8743
```

```js
// browser_evaluate after browser_navigate + browser_resize to the canvas size
() => {
  const poster = document.querySelector('.poster');
  const cb = poster.getBoundingClientRect().bottom;
  let maxBottom = 0, worst = null;
  poster.querySelectorAll('*').forEach(el => {
    const cs = getComputedStyle(el);
    if (cs.position === 'absolute' || cs.position === 'fixed') return; // out of flow
    const b = el.getBoundingClientRect().bottom;
    if (b > maxBottom) { maxBottom = b; worst = el; }
  });
  const overshoot = Math.round(maxBottom - cb);
  return { canvasBottom: Math.round(cb), contentBottom: Math.round(maxBottom),
           overshoot, all_ok: overshoot <= 0,
           worst: worst && (worst.className || worst.tagName) };
}
```

`all_ok: true` (overshoot ≤ 0) is required. Any positive `overshoot` is a **BLOCK** — cut content (per the P0/P1/P2 triage), never shrink the font.

---

## 3. Typography-floor probe (runtime — the only reliable way)

A static grep cannot catch this: sizes come from CSS vars, inheritance, and per-element overrides, so only the *computed* size is trustworthy. Maps to Non-Negotiable Rule 3 — the **body floor** (26px portrait / 22px landscape).

**Scope: body-reading surfaces ONLY.** Rule 3 is about not shrinking *body copy* to fit. The poster has legitimate smaller tiers below the body floor (`--fs-bullet` 24, `--fs-step` 22, `--fs-stat-label` 20, `--fs-ref`/`--fs-eyebrow` 17 — see the template `:root`); those are calibrated defaults, NOT body text, and **must not** be checked against the body floor or the probe cries wolf on every well-formed poster. Check only `.content-card p` and `.hero-sub`.

```js
() => {
  // body floor auto-selected by orientation (landscape canvas is wider than tall)
  const FLOOR = window.innerWidth > window.innerHeight ? 22 : 26;
  const BODY = ['.content-card p', '.hero-sub']; // true body copy only — NOT bullets/footer/captions
  const bad = [];
  BODY.forEach(sel => document.querySelectorAll(sel).forEach(el => {
    const px = parseFloat(getComputedStyle(el).fontSize);
    if (px + 0.05 < FLOOR) bad.push({ sel, px, text: el.textContent.trim().slice(0, 30) });
  }));
  return { floor: FLOOR, pass: bad.length === 0, violations: bad };
}
```

`pass: true` is required. A body surface below the floor is a **BLOCK** — raise it back to the calibrated default and cut content to fit, per Rule 3. The smaller tiers (bullets, flow steps, footer, eyebrows) have their own `:root` defaults and are out of scope for this probe.

---

## Order & stop condition

Run 1 → 2 → 3. The poster is "done" only when: scan returns no tokens, `all_ok: true`, and `pass: true` simultaneously. Report any failure to the user with the offending selector/text, not a generic "it overflowed".
