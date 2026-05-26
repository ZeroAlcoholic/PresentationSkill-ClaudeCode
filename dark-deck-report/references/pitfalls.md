# Pitfalls Log

Bugs encountered during real production runs of this skill, with root cause + fix.
Read this before debugging "why doesn't my CSS rule apply?"

---

## P-001 · Inline style specificity always wins

**Symptom:** Wrote `#s14 .flex-col { gap: 6px; }` to override default `gap: 10px`. The change didn't apply.

**Root cause:** The HTML had `<div class="flex-col" style="gap:10px">`. Inline `style` attributes have CSS specificity `1,0,0,0` — higher than any ID-scoped selector (`0,1,1,0`). The scoped rule was overridden by the inline style, not the other way around.

**Fix (in order of preference):**
1. Change the HTML to remove the inline style: `<div class="flex-col">` then let CSS control.
2. Change the HTML's inline value directly: `<div class="flex-col" style="gap:6px">`.
3. Add `!important` to the scoped rule: `#s14 .flex-col { gap: 6px !important; }`.

**Don't waste time on:** higher-specificity selectors. `html body div#s14 .flex-col` still loses to inline.

**Detection trick:** In DevTools Elements panel, click the element → Styles → if a rule has a strikethrough, it was overridden. Inline styles show at the very top with no selector.

---

## P-002 · Print mode breaks because of three layered hidings

**Symptom:** Print preview only shows slide 1; the other slides are blank or missing.

**Root cause:** Screen mode hides inactive slides with THREE mechanisms simultaneously:
1. `.slide { position: absolute; inset: 0; opacity: 0; pointer-events: none; }`
2. `.slide.active { opacity: 1; pointer-events: auto; }`
3. `body { overflow: hidden; }`

Print mode inherits all three by default. Result: every non-active slide stays at opacity 0 → blank pages.

**Fix:** The `@media print` block must reset all three:

```css
@media print {
  html, body { height: auto !important; overflow: visible !important; }
  #deck { position: static !important; display: block !important; }
  .slide {
    position: relative !important;
    opacity: 1 !important;
    pointer-events: all !important;
    display: flex !important;
    width: 100% !important;
    height: 100vh !important;
    page-break-after: always;
  }
  .slide:last-of-type { page-break-after: auto; }
}
```

**The `!important` is mandatory** because the screen styles use `position:absolute` etc. without `!important`, and CSS cascade resolution at print time would otherwise keep them.

---

## P-003 · Last slide creates a trailing blank page

**Symptom:** Printing a 4-slide brief produces 5 pages — the 5th is blank.

**Root cause:** `page-break-after: always` applies to every `.slide`, including the last one, which forces a break after the last content → empty page.

**Fix:**
```css
.slide:last-of-type { page-break-after: auto; break-after: auto; }
```

---

## P-004 · Brief slide 1 overflows by 9 px after a small content addition

**Symptom:** Added 5 words to slide 1's lede. Overflow check now shows 9 px.

**What I tried first (wrong):** Reducing margin on the lede block. Saved only 2 px because the margin wasn't actually 10 px — it was a `margin-bottom: 22px` I thought I had already reduced.

**Root cause discovery:** Measured padding via JS:
```javascript
() => {
  const s = document.getElementById('s1');
  return {
    paddingTop: getComputedStyle(s).paddingTop,
    paddingBottom: getComputedStyle(s).paddingBottom,
  };
}
// returned: { paddingTop: "44px", paddingBottom: "80px" }
```

The slide's own `padding-bottom: 80px` was the real culprit, set in the global `.slide` rule. Reducing it to `68px` saved 12 px and resolved overflow.

**Lesson:** When overflow is small and you've already exhausted content-level compression, measure the slide's padding directly. Don't assume which level the excess lives at.

---

## P-005 · DocLayNet cited but never referenced; LayoutParser referenced but never cited

**Symptom:** Reviewer noticed slide 12 lists `LayoutParser` as a recommended tool with no citation in slide 11. Slide 11 has a reference to `DocLayNet` that no slide actually uses.

**Root cause:** Reference list drifted out of sync with the recommendations as the deck was edited over multiple passes.

**Fix:** Treat refs and tools as a bidirectional check. Before delivery, scan: every named tool/paper in slides 5–13 must appear in slide 11; every entry in slide 11 must be cited in at least one earlier slide.

**Prevention:** When swapping out a tool in the recommendations, immediately update the references slide in the same edit pass.

---

## P-006 · "ECCV 2022" vs "arXiv 2022" — venue precision matters

**Symptom:** Reviewer (an ML PhD) flagged that "Donut: OCR-free Document Understanding Transformer (arXiv 2022)" looked sloppy. The paper was published at ECCV 2022, with an arXiv preprint.

**Fix:** Always cite the **venue**, with arXiv ID as a stable identifier. ECCV, CVPR, NeurIPS, ICML, ACL, EMNLP, ACM MM, ICDAR are venues. arXiv is a preprint server, not a venue.

**Pattern to use:**
```
Donut: OCR-free Document Understanding Transformer (ECCV 2022 · NAVER · arXiv:2111.15664)
```

---

## P-007 · "不引進中資工具" written explicitly — politically awkward

**Symptom:** First draft had a slide bullet that read "不引進百度系框架". Reviewer (the user) corrected: "這句不應該刻意寫 很怪".

**Fix:** Don't list what you exclude — just include what you chose. The selection itself communicates the policy. Use neutral framing:

- ❌ "不引進中資工具，避免 PaddleOCR 等百度系框架"
- ✅ "工具選型準則：開源授權清晰（Apache / MIT / BSD）· 可本地部署 · 活躍社群維護"

The positive framing is shorter, less defensive, and harder to misinterpret.

---

## P-008 · S12 table grew from 9 to 11 rows — table cell padding became the bottleneck

**Symptom:** Added 2 rows to slide 12's tool table (NumPy+SciPy, pikepdf+fonttools). Overflow jumped from 0 to ~25 px.

**Failed first attempt:** Reduced `.shead { margin-bottom }`. Saved 4 px, not enough.

**Working fix:** Reduced cell padding and font:
```css
#s12 td, #s12 th { padding: 3px 8px; font-size: 12px; }
```

**Why this worked:** Table padding compounds across N rows. Reducing top+bottom padding by 1 px each saves N × 2 px. With 11 rows, that's 22 px. Adding `font-size: 12px` (from 13px) compressed line height further.

**Lesson:** For dense tables, cell padding has multiplicative impact; per-slide overrides on `td, th` are the highest-leverage compression.

---

## P-009 · S8 overflowed 99 px after adding a single table row

**Symptom:** Added the `fonttools` row to slide 8's tool table (4 → 5 rows). Overflow exploded from 0 to 99 px.

**Why so much for one row?** The new row had a long multi-line description that pushed it to ~85 px tall. Plus the table was already at its budget.

**Fix:** Added a previously-missing scoped `td, th` rule (the slide didn't have one — it was using global defaults):
```css
#s8 td, #s8 th { padding: 4px 8px; font-size: 12px; line-height: 1.45; }
#s8 .card { padding: 8px 12px; }
#s8 p { font-size: 12.5px !important; }
#s8 .ib { padding: 7px 10px; font-size: 12px; margin-top: 4px; }
```

The `line-height: 1.45` (down from default ~1.55) was the highest-impact change because the multi-line description cells benefit most from tighter leading.

**Lesson:** When adding rows to a dense table, plan for a per-slide cell padding override at the same time.

---

## P-010 · Generic fonts make the deck feel AI-generated

**Symptom:** Used `font-family: Inter, sans-serif` in early drafts. The deck looked like every other ChatGPT-produced report.

**Fix:** Switched to `'IBM Plex Sans'` + `'Noto Sans TC'` for body, `'IBM Plex Mono'` for technical inflections. Plex has more character at small sizes — wider apertures on `a, e, c`, distinctive `Q` and `R`, optical-spacing-aware. The deck immediately felt more deliberate.

**Don't use:** `Inter`, `Roboto`, `Arial`, `Helvetica Neue` (alone), `system-ui`, `SF Pro Text`.

**Acceptable alternatives if Plex unavailable:** `IBM Plex Sans` (primary), `Source Sans 3` (fallback with similar character).

---

## P-011 · `scrollHeight - clientHeight` always returns 0 on slides

**Symptom:** Wrote the obvious overflow check `s.scrollHeight - s.clientHeight`. Got `0` for every slide, including a slide whose content I could visibly see being cut off at the bottom.

**Root cause:** `.slide { overflow: hidden }`. The browser clips `scrollHeight` to `clientHeight` when overflow is hidden — the difference is mathematically zero, by spec. The clip is invisible to the script.

**Fix:** Temporarily set `overflow: visible` AND measure the deepest descendant's bottom rect (NOT `scrollHeight`, NOT sum-of-children — both lie under the current vertical-fill contract). The canonical script is in [overflow-script.md](overflow-script.md) (single source of truth); [SKILL.md §Overflow verification workflow](../SKILL.md) and [verification-checklist.md §1](verification-checklist.md) both link there.

**Detection trick:** If your overflow script returns `0` (or a suspiciously constant headroom across all slides) when content visibly truncates, the script is broken — not the slide.

---

## P-012 · chrome-devtools-mcp viewport ≠ canvas

**Symptom:** Overflow script reported "all clear" but the printed PDF had truncated content at the bottom of slide 11.

**Root cause:** chrome-devtools-mcp opens a browser window where the **viewport** (the page-rendering area) is typically smaller than the requested window size — Chrome's UI chrome (title bar, address bar, devtools panel) eats vertical space. A `resize_page(1707, 960)` call may yield a viewport of `1282×670`. The slide uses `height: 100vh` → measures at 670, not 960. Content that fits 670 may overflow 960.

**Fix:** Inside the verification script, explicitly force the slide to the target canvas size:

```javascript
slide.style.width = '1707px';
slide.style.height = '960px';
void slide.offsetHeight;  // force reflow
```

Then measure the deepest descendant rect (NOT `scrollHeight` — overflow:hidden clips it; NOT sum-of-children — flex-fill stretches the body container). The canonical script in [overflow-script.md](overflow-script.md) does this.

**Lesson:** Never trust the viewport reported by `window.innerHeight` when the canvas is fixed. Always simulate the canvas dimensions before measuring.

---

## P-013 · `getComputedStyle(...).rowGap` returns NaN for non-grid layouts

**Status:** Historical. The current canonical script (deepest-descendant) does not compute padding/gap sums, so this exact bug can no longer recur here. The general lesson is still useful for any *new* DOM-measurement script.

**Symptom (historical):** The earlier sum-of-children overflow script returned `null` or `NaN` for `usedHeight` on slides whose root was `display: flex; flex-direction: column` with no explicit `gap`.

**Root cause:** `getComputedStyle(el).rowGap` returns `"normal"` (a string) when no `gap` property is set on a non-grid layout. `parseFloat("normal")` → `NaN`. Multiplying NaN through the sum poisoned every downstream number.

**Lesson (still active):** Any time you `parseFloat()` a computed style value, assume it could be a keyword like `"normal"` or `"auto"` — guard with `Number.isFinite()` before arithmetic. If you ever extend the verification script with new computed-style reads, apply this guard.

---

## P-014 · Absolute-positioned cover descendants inflate the bottom-rect scan

**Symptom:** Verification script reported `s1` (cover) with `overshoot: 240` — yet the cover visually fit perfectly in print and on screen.

**Root cause:** `#s1` has descendants outside normal flow:
- `#s1::before` and `#s1::after` (radial-bloom pseudo-elements, `position:absolute`)
- `.tc` (vertical timecode column, `position:absolute`, often placed off-canvas with negative offsets)

`getBoundingClientRect()` still returns real screen coordinates for absolutely-positioned elements — even though they take **zero** vertical space in flow. The original (sum-of-children) script added their heights to the total; the current (deepest-descendant) script would treat their `bottom` as the slide's content extent. Both inflate the cover measurement.

**Fix:** Filter `position: absolute` and `position: fixed` inside the descendant scan:

```javascript
for (const el of slide.querySelectorAll('*')) {
  const cs2 = getComputedStyle(el);
  if (cs2.position === 'absolute' || cs2.position === 'fixed') continue;
  // ...
}
```

This guard is present in the canonical script in [overflow-script.md](overflow-script.md).

**Lesson:** When sniffing layout via DOM measurement, always reconcile the box model with positioning context. Absolute/fixed children are *visible* but *invisible to flow* — they can mark either false overflow or false fit depending on the algorithm.

---

## P-015 · Measurement script left slides forced opaque after thrown exception

**Symptom:** After running the overflow script once successfully and once with a broken selector, the deck was visually broken — all slides simultaneously visible, stacked over each other.

**Root cause:** The script mutated `slide.style.opacity = '1'`, `position`, `height`, etc., then restored them at the end with `Object.assign(slide.style, orig)`. The restore line ran **only on the happy path**. When the loop iteration threw (e.g., the broken selector that returned null), restoration was skipped → forced styles persisted.

**Fix:** Wrap the mutation block in `try { ... } finally { Object.assign(slide.style, orig); }`. The `finally` runs whether or not an exception propagates.

**Lesson:** Any script that temporarily mutates the DOM and intends to restore must use try/finally. Don't rely on the script "always succeeding" — a single bad selector or detached node corrupts the page.

---

## P-016 · Templates shipped with baseline fonts too small for projection

**Symptom:** Real-world test of the skill in a meeting room — body text at 16px was readable on laptop but blurred at ~6m projection distance. Slide titles at 36px competed with proof lines at 16px (ratio too small to create hierarchy).

**Root cause:** Baselines were calibrated for desktop preview, not for actual projection. The implicit assumption "16px is web-standard, so it's fine" ignored that the deck's primary delivery channel is large-screen presentation.

**Fix:** Bumped all template baselines twice.
- **First pass**: body 16→18, h2 36→42, h3 19→22, table 15→17, stat 46→54, cover h1 72→78.
- **Second pass** (after the first real run showed mid-tier supporting text — card body, ref source, pipeline-stage caption — still felt undersized next to the bumped headlines): added an explicit hard floor of body ≥17px, mono labels ≥12.5px, references ≥13px. Brief and minimal got proportional bumps.

Current authoritative values live in [SKILL.md §Typography baselines](../SKILL.md). Do not chase the numbers in this entry — read the table there.

**Lesson:** When the deliverable's display context is presentation, calibrate at presentation, not at desktop. "Web-standard" is the wrong reference. And — bumping the headlines without bumping the *supporting* text creates a worse hierarchy than no bump at all; the eye notices the gap.

---

## P-017 · Slides clustered content at top, leaving empty lower half

**Symptom:** Real-world test showed every non-cover slide read top-heavy — heading + a couple of cards at the top, with 30–50% of the canvas empty at the bottom. The deck felt half-finished even when all content was correct.

**Root cause:** `.slide` was `display: flex; flex-direction: column` with no `justify-content` declared — browser default is `flex-start`. Children stacked from the top; empty space accumulated at the bottom.

**Fix:** Set `justify-content: space-between` on `.slide`. Cover overrides with `justify-content: center` to keep the title block centered. The print contract mirrors this so PDF stays visually identical.

**Lesson:** "Looks fine in dev" with sparse placeholder content is misleading — distribute content across the canvas axis-by-axis, don't rely on padding alone to balance the page.

---

## Quick-reference: which lever to pull when

| Problem | First lever to try |
|---------|-------------------|
| Slide overflows < 10 px | Reduce `.shead { margin-bottom }` |
| Slide overflows 10–30 px | Reduce `.card { padding }` |
| Slide with table overflows | Add `#sN td, th { padding: 4px 8px; font-size: 14px; }` (don't go below 14px in tables) |
| Slide with formula/code overflows | Reduce `line-height` in the code block |
| Print shows wrong page count | Inspect `@media print` rules — usually missing reset |
| CSS rule "doesn't apply" | Check for inline `style="..."` on the element |
| Reference looks weak | Add venue + institution + arXiv ID |
| Headline reads like a topic | Rewrite as a complete sentence |
| Slide looks top-heavy / empty lower half | Verify `.slide { justify-content: space-between }` is set; add inner `flex: 1` on the main block |
| Cover slide reports massive negative headroom | Script is not filtering `position:absolute` children (P-014) |
| Script left slides visually broken | Script lacks try/finally restore (P-015) |
| Body looks blurry from across the room | Bump baseline per §Typography baselines, not just one slide (P-016) |
