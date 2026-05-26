# Poster Types Reference

Decision guide for the three active template types.

---

## Type Detection

Scan input for these signals:

| Signal | Template |
|--------|----------|
| ≥ 2 key statistics / metrics | `poster-infographic-portrait.html` (default) |
| Process flow, numbered steps | `poster-infographic-portrait.html` |
| Business overview, market analysis | `poster-infographic-portrait.html` |
| Research summary, data-driven | `poster-infographic-portrait.html` |
| User says "landscape" / "horizontal" | `poster-infographic-landscape.html` |
| Conference display board, wide-format | `poster-infographic-landscape.html` |
| Single event, date, announcement | `poster-minimal.html` |
| Sparse content (≤ 3 short sections, no data) | `poster-minimal.html` |

**Default:** Always start with `poster-infographic-portrait.html` unless a clear signal says otherwise.

---

## Type A: Infographic — Portrait (Default)

**Canvas:** 1748×2480 CSS px · A1 portrait

**Best for:**
- Business one-pager (product, service, company overview)
- Market or competitive analysis summary
- Research / data summary with key findings
- Programme or initiative showcase
- Any content with 2–4 main sections + statistics

**Content zones:**
```
HERO       → eyebrow + headline + subtitle + org/date
STATS STRIP→ 2–4 key numbers (remove if < 2 stats)
CARDS 2×2  → up to 4 sections as content cards
FLOW STRIP → process steps (remove if no sequence)
CTA ZONE   → action or conclusion (optional)
FOOTER     → sources + QR
```

**Content length per card:** 40–70 words + 3 bullets max. Cut aggressively.

---

## Type B: Infographic — Landscape

**Canvas:** 2480×1748 CSS px · A1 landscape
**Playwright resize:** `browser_resize(2480, 1748)`

**Best for:**
- Conference display boards (horizontal track displays)
- Wide-format event signage
- Side-by-side comparison content
- When user explicitly requests landscape / horizontal

**Layout difference vs portrait:**
- Left column is the hero zone (fixed 680px wide)
- Right side: stats strip + 3-column cards + flow strip
- Body text smaller (18px vs 20px) — less vertical space
- Max 3 content cards (not 4)

**Content length per card:** 30–50 words + 3 bullets max (tighter than portrait).

---

## Type C: Minimal

**Canvas:** 1748×2480 CSS px · A1 portrait (default)
For landscape minimal: adjust `.poster { width: 2480px; height: 1748px; }`

**Best for:**
- Event / conference announcement
- Product or programme launch
- Award or milestone recognition
- Any content that is intentionally sparse for visual impact

**Content zones:**
```
TOP BAR    → eyebrow + org name
HEADLINE   → 1–2 line big statement (≤ 60 chars)
SUBTITLE   → one sentence
VISUAL ZONE→ placeholder for image / chart
POINTS     → exactly 3 key cards
EVENT STRIP→ date + venue + CTA (remove if not event)
FOOTER     → source + QR
```

**Key rule:** If content fills more than 60% of the poster, switch to infographic template instead.

---

## Theme Selection

| Context | Theme |
|---------|-------|
| Technical / data-heavy / research | Dark (default, no class change) |
| Public-facing, wide audience, print handout | Light (`body.light`) |
| Event / announcement for general audience | Light often better |
| Business internal / executive | Either — dark is more distinctive |

**How to apply light theme:** add `class="light"` to `<body>` tag.

---

## Content Length Limits

| Zone | Portrait A1 | Landscape A1 |
|------|-------------|--------------|
| Hero headline | ≤ 100 chars | ≤ 80 chars |
| Hero subtitle | ≤ 160 chars | ≤ 120 chars |
| Stat value | ≤ 8 chars | ≤ 8 chars |
| Stat label | ≤ 4 words | ≤ 3 words |
| Card body | 40–70 words | 30–50 words |
| Card bullets | 3 max, ≤ 12 words each | 3 max, ≤ 10 words each |
| Flow step label | ≤ 20 chars | ≤ 15 chars |
| Minimal headline | ≤ 60 chars | ≤ 50 chars |
| References | ≤ 4 | ≤ 3 |

**Trim rule:** Convert paragraphs → bullets first. Cut examples before cutting conclusions. Never shrink fonts.

---

## Visual Zone (Minimal template)

If the input has an image reference:
```html
<img src="./your-image.png" style="max-width:100%;max-height:100%;object-fit:contain;">
```

If no image:
- Leave placeholder and note in output: "Replace [VISUAL ZONE] placeholder with your image or chart"
- Or generate a simple inline SVG chart if input has data

---

## Extensibility

Academic multi-column templates (3-col portrait, 4-col landscape) are archived in:
`.archive/poster-maker-v1/templates/` — relative to your poster project folder.

To re-activate: copy files into `templates/` and add rows to the Templates table in SKILL.md.
