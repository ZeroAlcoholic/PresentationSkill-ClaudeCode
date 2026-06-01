# Typography Scale Reference

Poster typography is fundamentally different from screen typography. A poster viewed at 1–2m must use much larger body text than a web page viewed at 50cm.

---

## Viewing Distance → Minimum Readable Size

| Viewing Distance | Min Body Size (pt at print) | Min Body CSS px (A1 canvas) |
|------------------|-----------------------------|-----------------------------|
| 30cm (screen) | 8pt | 11px |
| 50cm (desk/phone) | 10pt | 14px |
| 1m (standing, reading) | 14pt | 20px |
| 1.5m (casual walk-by) | 18pt | 26px |
| 3m (across a room) | 28pt | 40px |
| 5m (hallway) | 48pt | 68px |

**Rule for posters:** Body copy minimum = **26px CSS portrait / 22px landscape** at A1 (targets ~1.5 m reading; matches SKILL.md Rule 3). Smaller tiers below (bullets, steps, footer, eyebrow) are calibrated sub-body roles, not body copy.

---

## Type Scale — A1 Portrait (1748×2480 CSS px)

These sizes match the actual CSS variables defined in `poster-infographic-portrait.html`. Printed pt = CSS px × 0.75.

```
Role              CSS var             CSS px    Printed pt    Usage
──────────────────────────────────────────────────────────────────────
hero title        --fs-hero-title     108px     81pt          Hero zone main headline
hero subtitle     --fs-hero-sub       36px      27pt          Hero zone one-liner (BODY tier)
hero stat value   (hardcoded)         96px      72pt          Dominant hero number (≤4 chars)
hero stat value   .long modifier      80px      60pt          Hero number 5–6 chars (e.g. "94.7%")
stat value        --fs-stat           88px      66pt          Stats strip numbers
stat label        --fs-stat-label     20px      15pt          Stat labels (MONO ALL-CAPS)
section heading   --fs-section-h      36px      27pt          Content card H3 titles
body text         --fs-body           26px      20pt          Card paragraphs (BODY floor)
bullet text       --fs-bullet         24px      18pt          List items in cards
flow step         --fs-step           22px      17pt          Flow strip step labels
CTA headline      --fs-cta            48px      36pt          Call-to-action zone
references        --fs-ref            17px      13pt          Footer citations
eyebrow           --fs-eyebrow        17px      13pt          MONO ALL-CAPS category tags
```

CSS variables (copy exactly from template `:root`):
```css
:root {
  --fs-hero-title: 108px;
  --fs-hero-sub: 36px;
  --fs-stat: 88px;
  --fs-stat-label: 20px;
  --fs-section-h: 36px;
  --fs-body: 26px;
  --fs-bullet: 24px;
  --fs-step: 22px;
  --fs-cta: 48px;
  --fs-ref: 17px;
  --fs-eyebrow: 17px;
}
```

---

## Type Scale — A1 Landscape (2480×1748 CSS px)

Landscape has less vertical space — every tier is tighter than portrait. Values below match `poster-infographic-landscape.html` `:root` exactly.

```
Role              CSS var             CSS px    Printed pt    Note
──────────────────────────────────────────────────────────────────
hero title        --fs-hero-title     80px      60pt          Landscape left-col hero
hero subtitle     --fs-hero-sub       28px      21pt          BODY tier (landscape)
stat value        --fs-stat           68px      51pt          Stats cells
stat label        --fs-stat-label     16px      12pt          MONO ALL-CAPS
section heading   --fs-section-h      26px      20pt          3-col card titles
body text         --fs-body           22px      17pt          Card body (BODY floor, landscape)
bullet text       --fs-bullet         20px      15pt          List items
flow step         --fs-step           18px      14pt          Flow strip
CTA headline      --fs-cta            32px      24pt          Call-to-action
references        --fs-ref            15px      11pt          Footer strip
eyebrow           --fs-eyebrow        14px      11pt          MONO category tag
```

---

## Type Scale — A2 Portrait (1240×1754 CSS px)

Scale all A1 portrait values by 0.71 (√2 ratio):

```
Role              CSS px (A2)
────────────────────────────
hero title        77px
stat value        62px
section heading   26px
body text         18px  ← still ≥ 16px reject line; check at ~1m viewing distance
```

---

## Line Height & Letter Spacing

```css
/* Body text */
line-height: 1.6;
letter-spacing: 0.005em;

/* Headings */
.hero-title { line-height: 1.05; letter-spacing: -0.03em; }
h3 { line-height: 1.3; letter-spacing: 0; }

/* Monospace labels / eyebrows */
.hero-eyebrow { letter-spacing: 0.22em; text-transform: uppercase; }
.hero-stat-label { letter-spacing: 0.18em; text-transform: uppercase; }
```

---

## Font Loading

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Sans:wght@400;500;600;700&family=IBM+Plex+Mono:wght@400;500&family=Noto+Sans+TC:wght@400;500;700&display=swap" rel="stylesheet">
```

Font stack:
```css
body {
  font-family: 'IBM Plex Sans', 'Noto Sans TC', 'Helvetica Neue', Arial, sans-serif;
}
.hero-eyebrow, .hero-stat-value, .hero-stat-label, .sv, .sl, .footer-ref {
  font-family: 'IBM Plex Mono', 'Cascadia Code', monospace;
}
```

**Chinese/CJK characters:** Noto Sans TC covers Traditional Chinese (TC). Font stack falls back gracefully to system sans-serif if CDN is unreachable — layout is preserved but appearance differs.

---

## Weight Semantics

| Weight | Usage |
|--------|-------|
| 400 (Regular) | Body text, captions, references |
| 500 (Medium) | Hero subtitle, stat descriptions |
| 600 (SemiBold) | Section headings (H3), stat labels, eyebrow |
| 700 (Bold) | Hero title, stat values, CTA button text |

Never use 300 (Light) — insufficient contrast at poster viewing distances.

---

## Typographic Rules

1. **Hero title**: One complete declarative sentence or claim. Max 2 lines. `font-weight: 700`. Use `<span class="accent">` for the key word.
2. **Section headings (H3)**: `font-weight: 600`, `letter-spacing: 0`. Not ALL-CAPS — sentence case only.
3. **Body paragraphs**: 40–70 words per card. Never use `<br>` — let CSS wrap naturally.
4. **Bullet lists**: Max 3 bullets per card, ≤12 words each. Use `line-height: 1.5`, `gap: 8px` between items.
5. **Numbers in body**: Use tabular figures: `font-variant-numeric: tabular-nums` on stat containers.
6. **Units**: Always add units to stat values. Either in stat-label or inline in stat value (e.g. `96%`).

---

## Anti-Patterns

- Body copy below the A1 floor (26px portrait / 22px landscape) → reject (see Rule 3); 16px or below is unreadable at 1m in any case
- Using system serif fonts (Times New Roman, Georgia) — clashes with IBM Plex aesthetic
- All-caps body paragraphs — legible for ≤4 words only
- `italic` for body text — unreadable at viewing distance
- `<br>` in card body or bullets — causes line-break anchoring that breaks at different widths
- Hero stat value > 6 chars at default 96px → overflows stat block
