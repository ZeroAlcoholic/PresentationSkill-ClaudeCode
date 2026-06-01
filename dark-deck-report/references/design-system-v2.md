# Design System v2 — Editorial Weight & Marginalia

A bolted-on extension to the v1 tokens (do **not** remove v1; v2 lives alongside). Two upgrades:

1. **Card weight hierarchy** — replace the uniform `.card` grid with a `hero / default / quiet` triad so one element per zone earns the eye.
2. **Editorial marginalia** — page numbers, system fingerprint, rotated role label, figure numbers, 1px hairline rules. The slide should feel **printed**, not generated.

Why: v1 ships strong identity (Plex + dark forensic green) but every card carries the same visual weight and every slide looks identical at a glance. v2 borrows the print-editorial vocabulary (slide number top-right, fingerprint bottom-left, role label rotated on the spine) to make slides individually memorable while staying within the same color/type system.

This file is the **canonical reference** for both `dark-deck-report` and `poster-maker` — the latter should adopt the same primitives where the aesthetic carries over.

---

## 1. Tokens (additions only — v1 stays)

```css
:root {
  /* ── v2 type roles ── */
  --fs-display: 84px;     /* hero card headline (deck) — was 22px on .card */
  --fs-micro: 11px;       /* page numbers, fingerprint, figure labels, eyebrows (.scat) */
  --fs-marg: 10px;        /* rotated edge marginalia */

  /* ── v2 hairlines & weights ── */
  --w-rule: 1px;          /* horizontal rules between zones */
  --w-rule-heavy: 2px;    /* hero-card border */

  /* ── v2 spacing (strict 8px grid) ── */
  --s-1: 4px;  --s-2: 8px;  --s-3: 12px; --s-4: 16px;
  --s-5: 24px; --s-6: 32px; --s-7: 48px; --s-8: 64px;

  /* ── marginalia color (faded tertiary) ── */
  --marg-fg: rgba(159,191,166,0.42);  /* derived from --tm @ 42% */
  --marg-rule: rgba(31,58,37,0.55);   /* derived from --bd @ 55% */
}
/*
  FLOOR CARVE-OUT — scoped exception to SKILL.md §Typography baselines.
  The micro/marg tier (--fs-micro 11px, --fs-marg 10px) is INTENTIONALLY below
  the 12.5px mono floor. That floor governs *reading* mono (table cells,
  proof lines, reference text); the micro tier is non-reading chrome —
  eyebrows (.scat), page numbers, fingerprint, figure tags, rotated role
  label. They are high-tracking labels scanned, not read, and exist to make
  the slide feel printed. The 17/12.5/13 floors still bind every body,
  table, and reference surface in v2; do NOT "raise" the micro tier to 12.5.
  (The typography-floor probe in overflow-script.md excludes these selectors
  for v2 — see its note.)
*/
:root {

  /* ── hero card ── */
  --hero-pad: var(--s-6) var(--s-7);
  --hero-radius: 4px;     /* squarer than v1's 10px — print feel */
  --hero-shadow: 0 0 0 1px var(--bg), 0 8px 32px rgba(74,222,128,0.10);

  /* ── quiet card (no border) ── */
  --quiet-pad: var(--s-4) 0;
  --quiet-bord: 1px solid var(--bd);
}
```

---

## 2. Card hierarchy primitives

### `.card-hero` — feature card (1 per zone max)

```css
.card-hero{
  background:var(--sf);
  border:var(--w-rule-heavy) solid var(--gl);
  border-radius:var(--hero-radius);
  padding:var(--hero-pad);
  box-shadow:var(--hero-shadow);
  position:relative;
}
.card-hero[data-fig]::before{
  content:'FIG. ' attr(data-fig);
  position:absolute;top:-9px;left:24px;
  background:var(--bg);padding:0 10px;
  font-family:'IBM Plex Mono',monospace;
  font-size:var(--fs-micro);
  letter-spacing:0.22em;color:var(--gl);
}
.card-hero .ch-eyebrow{
  font-family:'IBM Plex Mono',monospace;
  font-size:13px;letter-spacing:0.18em;
  text-transform:uppercase;color:var(--gl);
  margin-bottom:var(--s-3);
}
.card-hero .ch-headline{
  font-size:var(--fs-display);
  line-height:1.04;letter-spacing:-0.022em;
  font-weight:600;color:var(--tx);
  margin-bottom:var(--s-4);
}
.card-hero .ch-body{
  font-size:18px;color:var(--tm);
  line-height:1.55;max-width:62ch;
}
```

### `.card-quiet` — supporting card (no border, hairline only)

```css
.card-quiet{
  background:transparent;
  border:none;border-radius:0;
  border-top:var(--quiet-bord);
  padding:var(--quiet-pad);
}
.card-quiet .cq-eyebrow{
  font-family:'IBM Plex Mono',monospace;
  font-size:var(--fs-micro);letter-spacing:0.16em;
  text-transform:uppercase;color:var(--td);
  margin-bottom:var(--s-1);
}
.card-quiet .cq-body{
  font-size:15px;color:var(--tm);line-height:1.55;
}
```

### `.card` (v1) — keep unchanged; use for table-of-equals contexts only

```
Rule of thumb per slide:
  ≥1 .card-hero  (the takeaway)
  ≤3 .card-quiet (the support)
  Avoid mixing 3+ .card siblings — pick a primary.
```

### Grid: `.g-feature` (1 hero + 2 quiets, asymmetric)

```css
.g-feature{
  display:grid;
  grid-template-columns:1.8fr 1fr;
  gap:var(--s-6);
  align-items:start;
}
.g-feature .col-quiet{
  display:flex;flex-direction:column;
  gap:var(--s-3);  /* quiet cards stack with hairlines */
}
```

---

## 3. Marginalia system

Every slide gets four pieces of frame:

| Piece | Position | Class | Content |
|---|---|---|---|
| Page number | top-right (24px in) | `.slide-num` | `01 / 04` (current/total, mono) |
| System fingerprint | bottom-left (76px in, baseline) | `.slide-fp` | `REPORT-SYNTHESIS · v0.1 · 2026-05` |
| Role label | left edge, rotated -90° | `.slide-role` | `COVER` / `HERO` / `SELECTION` / `REFERENCES` |
| Top rule | between shead and body | `.slide-rule` | 1px solid var(--marg-rule), full width |

```css
.slide-num{
  position:absolute;top:20px;right:28px;z-index:5;
  font-family:'IBM Plex Mono',monospace;
  font-size:var(--fs-micro);letter-spacing:0.18em;
  color:var(--td);
}
.slide-num .of{color:var(--bd2);}

.slide-fp{
  position:absolute;bottom:18px;left:76px;z-index:5;
  font-family:'IBM Plex Mono',monospace;
  font-size:var(--fs-micro);letter-spacing:0.14em;
  color:var(--td);
}

.slide-role{
  position:absolute;left:18px;top:50%;z-index:5;
  transform:translateY(-50%) rotate(-90deg);
  transform-origin:left center;
  font-family:'IBM Plex Mono',monospace;
  font-size:var(--fs-marg);letter-spacing:0.36em;
  text-transform:uppercase;color:var(--marg-fg);
  white-space:nowrap;
}

.slide-rule{
  height:1px;width:100%;background:var(--marg-rule);
  margin-bottom:var(--s-5);
}
```

**Inline cross-references** — `<span class="xref">→ FIG. 02</span>` for in-prose anchors:
```css
.xref{
  font-family:'IBM Plex Mono',monospace;
  font-size:0.86em;letter-spacing:0.08em;
  color:var(--gl);padding:0 4px;
  border-bottom:1px dashed var(--bd2);
}
```

---

## 4. v1 → v2 visual diff (deck-brief, conceptual)

```
v1 cover:                            v2 cover:
┌────────────────────────────────┐   ┌────────────────────────────────┐
│        [ centered title ]      │   │ COVER ▍              01 / 04   │
│        [   3 even cards   ]    │   │ ──────────────────────────────  │
│                                │   │  REPORT-SYNTHESIS              │
│                                │   │                                │
│                                │   │  ▌ HEADLINE BIG (84px display) │
│                                │   │  ▌ ──                          │
│                                │   │  ▌ One quiet sentence below.   │
│                                │   │                                │
│                                │   │  ┌─FIG.01──hero card──┐  ●     │
│                                │   │  │ takeaway claim      │  ●    │
│                                │   │  └────────────────────┘  ●     │
│                                │   │   (right column: 2 quiet refs) │
│                                │   │ ──────────────────────────────  │
└────────────────────────────────┘   │ REPORT-SYNTHESIS · v0.1 · 26-05│
                                     └────────────────────────────────┘
                                       ↑ rotated "COVER" on left edge
```

The v2 version reads as a **page** — header / body / footer with a printed identity — not a slide of stacked cards.

---

## 5. Application rules

- **One hero per slide.** If you need two emphasis points, pick the stronger; demote the other to a quiet card or a footnote.
- **Marginalia is non-negotiable.** Page number + fingerprint + role label appear on every slide. They cost ~30 lines of CSS for the whole deck and pay back identity on every page.
- **Hairlines over boxes.** `.card-quiet` uses border-top only; the cluster reads as a *list* held by a rule, not a *stack* of containers.
- **Display type wants air.** `.card-hero .ch-headline` at 84px needs ≥80px vertical breathing space above and below; do not pack hero cards into tight grids.
- **Animations.** Marginalia loads instantly (no `reveal-up`); hero card uses the existing 540ms reveal. The frame should feel pre-printed; the content should feel newly arrived.

---

## 6. Versioning & rollout

- v2 lives in `templates/deck-brief-v2.html` alongside v1. Renderer skill picks by frontmatter flag (future: `delivery.design_system: v2`).
- Other v2 templates (`deck-full-v2`, `deck-minimal-v2`, poster v2 set) roll out incrementally — only when the v1 variant is touched for other reasons OR a user explicitly requests upgrade.
- v1 stays in repo as the conservative-default; v2 is the editorial-default for new decks once stable.
