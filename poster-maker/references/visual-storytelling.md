# Visual Storytelling Reference

Infographic theory and visual communication principles for poster review.

---

## Narrative Arc — Poster Thirds

Every poster should map to three acts:

```
TOP 23%   → HOOK        Make the viewer stop. One bold claim + dominant number.
MIDDLE 60% → BUILD       Evidence. Stats, sections, process. Prove the claim.
BOTTOM 17% → RESOLUTION  What to do. CTA, flow steps, sources, QR.
```

**Portrait**: HERO = hook / STATS+CARDS = build / FLOW+CTA+FOOTER = resolution
**Landscape**: Left hero col = hook / Right stats+cards = build / Footer row = resolution

Viewers decide in 5 seconds whether to read further. The hook must work without the build.

---

## Eye-Path Patterns

### Portrait → F-Pattern
```
← Left edge anchor (eyebrow, section titles)
→ First scan: full-width hero (top bar)
→ Second scan: stats strip
→ Left-side bias: Card 1 > Card 3 > Card 2 > Card 4
```

- Put the most important card **top-left** (Card 1)
- Put complementary detail **bottom-right** (Card 4 — often read last or skipped)
- Eyebrow, section headers, and bullet arrows should align on left edge to reinforce the F-path

### Landscape → Z-Pattern
```
① Top-left: hero eyebrow + start of title
  → ② Top-right: first stat in strip or card 1
③ Diagonal cross
  → ④ Bottom-left: flow step 1 or CTA
    → ⑤ Bottom-right: QR code / footer
```

- Left hero column (680px / 27.4% of canvas) serves as Z-pattern anchor point ✓
- Right content starts at top-right (Z point ②)
- Footer reads left-to-right as final Z stroke ✓

---

## 5-Second Rule

The hero element must communicate the core message before the viewer's attention drops.

**Target canvas coverage:**
- Hero zone: **22–30%** of total canvas height/area
- Portrait: 560px / 2480px = 22.6% ✓ (minimum threshold met)
- Landscape: 680px / 2480px = 27.4% wide ✓

**Hero must contain:**
1. Category label (eyebrow) — tells viewer "this is for you"
2. Main claim (title) — the one thing to remember
3. Dominant number (hero stat) — makes abstract concrete

**If hero stat is absent**, increase title font-weight or add a pull-quote in its place.

---

## LATCH Organizing Principle (Richard Saul Wurman)

Choose one and apply consistently to all cards:

| Principle | Best for | Card order |
|-----------|----------|------------|
| **L**ocation | Geographic, regional, market comparisons | Map order or proximity |
| **A**lphabet | Reference, glossary, named features | A→Z |
| **T**ime | Roadmaps, before/after, processes | Chronological; use FLOW STRIP |
| **C**ategory | Grouping by type/theme — **most common** | By importance (top-left = most impactful) |
| **H**ierarchy | Rankings, priority, org charts | Largest/most important first |

**Default for business infographics: Category (C)**
Order cards so Card 1 = most impactful finding, Card 4 = supporting/context.

---

## Gestalt Principles Applied

**Proximity** — elements that belong together should be visually grouped:
- Icon + title in same `.icon-row` (gap: 14px)
- Stat value + label + description as a unit (gap: 6px)

**Similarity** — same element type should look the same:
- All card headers: same font-size (26px), same weight (600)
- All eyebrow labels: same IBM Plex Mono, same letterspacing (0.22em)

**Continuity** — the eye follows lines and paths:
- Left-aligned body text creates a vertical reading rail
- Horizontal dividers (border-bottom: 1px) create horizontal reading bands

**Figure-Ground** — important elements must contrast against the background:
- Stats: 76px mono on dark surface → high contrast ✓
- Body text: 20px on --sf2 → contrast must be ≥ 4.5:1 (WCAG AA)

**Common Fate** — elements that move or flow together appear related:
- Flow step arrows (→) must be smaller than step numbers to avoid competing

---

## Semantic Colour Encoding

Apply colours by **meaning**, never decoratively:

| Token | Color | Semantic meaning |
|-------|-------|-----------------|
| `--gl` | Green `#4ade80` | Positive, growth, success, recommendation |
| `--cyl` | Cyan `#22d3ee` | Data, information, neutral finding |
| `--aml` | Amber `#fbbf24` | Caution, cost, friction, secondary risk |
| `--pul` | Purple `#a78bfa` | Synthesis, insight, meta-level comment |
| `--rl` | Red `#f87171` | Problem, risk, negative metric |

**60-30-10 rule:**
- 60%: background surfaces (--bg, --sf, --sf2)
- 30%: body text and structural elements (--tx, --tm, --td, --bd)
- 10%: accent colours (--gl, --cyl, --aml, --pul) — sparingly, for meaning only

---

## WCAG Contrast Targets

Minimum standards for poster text legibility:

| Text type | Minimum ratio | Target |
|-----------|---------------|--------|
| Body text (≥18px) | 4.5:1 (AA) | 7:1 (AAA) |
| Large text (≥24px bold) | 3:1 | 4.5:1 |
| Stat values (76–96px) | 3:1 | 4.5:1 |
| Footer captions (15px) | 4.5:1 | — |

**Dark theme approximate ratios (pass/fail):**
- `--tx #e8f5ea` on `--bg #060d07` ≈ 17:1 ✓ body text
- `--gl #4ade80` on `--bg #060d07` ≈ 8.9:1 ✓ accent text
- `--cyl #22d3ee` on `--bg #060d07` ≈ 7.2:1 ✓ accent text
- `--aml #fbbf24` on `--bg #060d07` ≈ 10.6:1 ✓ accent text
- `--tm #9fbfa6` on `--sf2 #0f2113` ≈ 5.4:1 ✓ body text (minimum pass)
- `--td #6b8a72` on `--sf #0c1a0e` ≈ 3.8:1 ✗ captions only (borderline — acceptable for non-essential metadata)

**Light theme:** re-verify contrast when using `body.light` — all light-theme tokens recalibrated for similar ratios.

---

## Data-Ink Ratio (Tufte)

Every pixel of ink on a poster should encode information. Remove:
- Decorative borders that don't separate content
- Gradient overlays that don't direct the eye
- Empty stat cells (remove the cell, not just blank it)
- Placeholder text ("Lorem ipsum" equivalent)

Keep:
- Content-separating borders (1px solid --bd between cards)
- Background glows that indicate the hero anchor zone
- Icon color tiles that encode card category

**Target**: > 60% of visual elements should carry semantic information.

---

## White Space Rules

- **Minimum breathing room**: 15% of total canvas should be visually empty
- **Padding floor**: 32px minimum inside any card; 56px for hero and footer zones
- **Never reduce padding to fit content** — cut content instead (see SKILL.md trim rules)
- **Gutter between cards**: use border lines, not gap — prevents content area shrinkage

---

## Pre-Flight Visual Checklist

Before writing the output file, verify:

- [ ] Hero stat exists (remove if no key number — do not leave placeholder)
- [ ] All four cards have different accent colours (green / cyan / amber / purple)
- [ ] Bullet arrow colour follows card order (Card 1=green, 2=cyan, 3=amber, 4=purple)
- [ ] Flow strip removed if no sequential process in the content
- [ ] CTA zone removed if no call-to-action in the content
- [ ] Stats strip removed if fewer than 2 numeric stats
- [ ] No `{{` tokens remain in output
- [ ] Footer has at least one real source citation
- [ ] QR block shows the URL even if SVG not generated (text fallback)
- [ ] Canvas matches @page declaration (A1 portrait: 1748×2480, landscape: 2480×1748)
- [ ] `overflow: hidden` on every section container

**Eye-path sanity check:**
- [ ] Most important information is top-left of content area
- [ ] Hero communicates the core message without reading the cards
- [ ] Colour accents follow semantic rules (no amber for positive metrics)
