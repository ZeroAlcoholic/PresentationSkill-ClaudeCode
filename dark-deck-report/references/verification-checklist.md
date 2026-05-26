# Pre-Delivery Verification Checklist

Run through this before reporting a deck as complete. Every item must pass.

---

## 1. Overflow check (mandatory, every slide, canvas 1707×960)

> **Do NOT use `slide.scrollHeight - slide.clientHeight`.** Slides are styled `overflow:hidden`, which clips `scrollHeight` to `clientHeight` — the difference is always 0, even when content overflows. Also: the chrome-devtools-mcp viewport is typically smaller than 960px due to window chrome, so `clientHeight` lies. Use the script below, which **forces** the slide to canvas dimensions before measuring the deepest descendant rect.
>
> **Do NOT use sum-of-children either.** With the vertical-fill contract (`.slide { justify-content: space-between }` + body siblings `flex: 1 1 auto`), the body container *stretches* to fill remaining space. A sum-of-children measure then always reports near-zero headroom regardless of natural content size, hiding real overflow within the stretched container. Use the **deepest-descendant** check below.

Open the deck in Chrome via chrome-devtools-mcp, then run the canonical script — copy verbatim from [overflow-script.md](overflow-script.md), which is the single source of truth. Returns `{ canvas, results, failures, all_ok }`.

**Pass criteria** (in this order):

1. `all_ok === true` — `overshoot === 0` for every slide. Any positive overshoot blocks delivery.
2. No `NaN` values anywhere in the result. NaN means the script encountered something it can't measure → fix the script, not the slide.
3. `contentBottom` between ≈ 820 and ≈ 940 on body slides (canvas − bottom padding ≈ 898). Below 820 → slide under-filled (top-heavy, violates §Vertical fill contract). At 940+ → tight; further additions will overflow.
4. Cover slide (`#s1`) is exempt — its `contentBottom` is naturally ~700 because the cover uses `justify-content: center`, not space-between.

See [overflow-script.md](overflow-script.md) for the rationale behind each defensive move (try/finally, absolute-filter, deepest-descendant).

If `overshoot > 0`, apply compression in this order (least → most invasive):

| Order | Lever | Magnitude |
|------:|-------|-----------|
| 1 | `.shead { margin-bottom }` | 26 → 16 saves ~10 px |
| 2 | `.card { padding }` | 16 20 → 12 16 saves ~8 px |
| 3 | Table `td,th { padding; font-size; line-height }` | Most aggressive — saves 30–100 px on dense tables; `line-height: 1.45` is the biggest single lever on multi-line cells |
| 4 | Inter-block `gap` in flex/grid | 18 → 12 saves N×6 px |
| 5 | `<h3>` margin-bottom | 10 → 4 saves N×6 px |
| 6 | Slide-level `#sN { padding-top / padding-bottom }` | Last resort |

Add overrides to the `PER-SLIDE DENSITY OVERRIDES` block at the bottom of `<style>`, scoped with `#sN`. Remember: inline `style="..."` wins against scoped CSS without `!important` (see pitfalls P-001).

**If overflow exceeds 80px**: cut content, do not compress further. Compression below the published baselines (body 17px floor, mono labels 12.5px floor, references 13px floor — see SKILL.md §Typography baselines) breaks projection readability. The deck is meant to be projected.

---

## 2. Print / PDF check

1. Open the deck in Chrome.
2. `Ctrl+P` (or `Cmd+P`).
3. **Destination:** Save as PDF.
4. **Layout:** Landscape.
5. **Paper size:** A4.
6. **Margins:** None / Default to 0.
7. **Scale:** Default (100%).
8. **Background graphics:** ON.
9. Verify the preview shows exactly **N pages for N slides** — no orphan pages, no truncated content.
10. Save the PDF. Open it. Confirm all 14 (or 4) slides render with content intact.

If preview shows wrong page count or split content:
- Missing slides → the `@media print` rules aren't loading (check selector order in `<style>`).
- Extra blank pages → `body { overflow:hidden }` reset is missing.
- Truncated content → some slide overflowed but the print mode hid it; fix overflow first.

---

## 3. Reference verification

For every entry in the references slide:

- [ ] Title is the full paper / framework name (not a marketing tagline)
- [ ] Venue is real (`ACM MM 2022`, `ECCV 2022`, `ICDAR 2021`, `NeurIPS 2023` — not "arXiv 2022")
- [ ] Year matches the venue's publication year
- [ ] Institution is named (Microsoft / Intel Labs / UPenn / IBM Research / etc.)
- [ ] Stable identifier present (`arXiv:2204.08387`, DOI, repo path)
- [ ] URL resolves (spot-check 2–3 randomly)
- [ ] License is named explicitly (`Apache 2.0`, `MIT`, `BSD`, `MPL-2.0`) — never just "open source"

If a venue is unverified: use WebSearch / WebFetch to confirm before publishing.

---

## 4. Headline-as-conclusion audit

Walk every `.shead h2`. Each must be a **complete sentence stating the takeaway**, not a topic.

| ❌ Topic | ✅ Conclusion |
|--------|-------------|
| "Architecture Overview" | "防偽整合每個選型都有商用系統先例與學術依據 — 不是實驗性研究" |
| "Tool Selection" | "全棧開源優先（Apache / MIT / BSD），每項均有替代方案" |
| "Roadmap" | "三階段交付，Phase 0 在 6 週內達到 80% 拒絕率覆蓋" |

If a headline is just a topic, the slide isn't done.

---

## 5. Accent color semantic audit

The accent colors carry meaning. Spot-check 3 random slides:

| Color | Means | Wrong uses to flag |
|-------|-------|------|
| Green `--gl` | OK / primary / stage marker | Don't use for "info" — that's cyan |
| Amber `--aml` | Anomaly / warning / soft alert | Don't use because "amber looks nice" |
| Cyan `--cyl` | Info / data / extraction | Don't use for "success" — that's green |
| Purple `--pul` | Synthesis / commercial / advanced | Don't use for "important" generically |
| Red `--rl` | Reject / hard fail / critical | Don't use for emphasis without alert meaning |

A reader scrolling through the deck should calibrate to this system after slide 3.

---

## 6. Typography sanity check

- [ ] Body font is `IBM Plex Sans` + `Noto Sans TC` — not `Inter`, not `system-ui`
- [ ] Mono font is `IBM Plex Mono` (or `JetBrains Mono`) — not just `monospace`
- [ ] Tabular numerics are on in tables: `font-variant-numeric: tabular-nums` (already global in templates)
- [ ] Font loading link is in `<head>` and renders before content
- [ ] No emoji in `<h2>` headings (allowed inline in body if the user explicitly asked)
- [ ] **Hard floors hold across all slides** (see SKILL.md §Typography baselines):
  - Body content (p, ul, ol, card body, info-box) ≥ **17px**
  - Mono labels (eyebrows, badges, stat labels, pipeline-stage titles) ≥ **12.5px**
  - References (`.rt`, `.rsrc`, `.rl2`) ≥ **13px**
- [ ] No per-slide override has reduced a token below its template baseline to "fit more text" — cut content instead

---

## 7. Screenshot verification on dense slides

For any slide with a table > 6 rows, formula block, or > 8 references:

1. Use chrome-devtools-mcp `take_screenshot` after navigating to that slide.
2. Visually confirm:
   - All rows are readable at projection distance (font ≥ 13px effective for refs, ≥ 17px for body)
   - No truncated text or `…` cutoffs
   - Color contrasts are sufficient (especially amber-on-dark, purple-on-dark)
   - Tables don't overflow horizontally
   - **Vertical balance**: content does not cluster in the top half of the slide with empty lower half (violates §Vertical fill contract)

---

## 8. Cross-deck consistency

If you produced both a full deck and a brief:

- [ ] Both use the same design tokens (no token drift)
- [ ] Both cite the same references with the same venue strings
- [ ] Brief's claims are a strict subset of full deck's claims (no new content in brief)
- [ ] Both pass overflow + print checks independently

---

## 9. Navigation & keyboard

- [ ] Arrow Left / Right works
- [ ] Space advances
- [ ] Home / End jump to first / last slide
- [ ] Progress bar moves
- [ ] Counter updates ("N / TOTAL")
- [ ] Nav chrome is hidden in print preview

---

## 10. Final smoke test

1. Hard reload (`Ctrl+Shift+R`) to bypass cache.
2. Click through every slide once.
3. Print preview, save PDF, open PDF, click through.
4. Done.
