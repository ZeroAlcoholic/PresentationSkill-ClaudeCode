# Visual Storytelling Principles

The six load-bearing moves that give this deck its voice. Every move serves a narrative function — none are decorative.

---

## 1. Headline IS the conclusion

The slide title is a **complete sentence stating the takeaway**, not a topic label. Sub-title carries the proof.

| ❌ Topic | ✅ Conclusion |
|--------|-------------|
| "Architecture Overview" | "整合方案在既有 S1→S5 主軸上新增兩條支線 — 非新管線" |
| "Q3 Performance Review" | "Q3 outperformed plan by 18% on retention, missed by 4% on ARPU" |
| "Methodology" | "我們以 1,200 件樣本、雙盲標註、Krippendorff α=0.83 重新標定基準" |
| "Tool Selection" | "全棧開源優先（Apache / MIT / BSD），每層皆有可替換方案" |

**Why this matters.** A reader paging through a deck on their phone reads only titles. If every title is a topic, they learn nothing. If every title is a conclusion, they get the entire argument in 14 sentences.

**Audit rule.** Walk every `.shead h2` before delivery. If any reads like a topic, the slide isn't done.

---

## 2. Semantic colour discipline

Five accent colours have **fixed semantic meanings**. Using them otherwise is a bug.

| Token | Colour | Meaning | Wrong uses |
|-------|--------|---------|------------|
| `--gl` green `#4ade80` | OK / primary / stage marker / approved | "info" callouts — use cyan |
| `--aml` amber `#fbbf24` | Anomaly / warning / soft alert | "It looks nice" — pick by meaning |
| `--cyl` cyan `#22d3ee` | Info / data / extraction / link | "Success" — that's green |
| `--pul` purple `#a78bfa` | Synthesis / commercial / advanced | "Important" generically |
| `--rl` red `#f87171` | Reject / hard fail / critical | Emphasis without alert meaning |

A reader scrolling through 14 slides should calibrate to this system by slide 3. After that, the colour itself carries information — a reader sees a red badge and *knows* "this is a critical / reject signal" before reading the text.

**Audit rule.** Spot-check 3 slides. If any colour usage doesn't match the meaning above, fix it.

---

## 3. Atmospheric depth (cover slide)

The cover slide gets four layers of depth — most decks have one (flat text on flat background).

1. **Radial bloom** — top-right green halo + bottom-left cyan halo. Sets the forensic / observatory mood.
2. **Vertical timecode column** — rotated `-90°` on the left edge. A quiet visual anchor; reads like a navigation rail.
3. **Accent ornament** — 64×2px glowing green bar above the eyebrow. Provides a starting line for the eye.
4. **Pulsing dot** — 6px glowing green circle prefixing the eyebrow text, breathing every 2.4s. Tells the viewer the deck is "live" / current.

The headline includes a `<span class="accent">` to highlight one or two key words in green — typographic emphasis without bold or underline.

**Why this matters.** The cover sets the contract with the reader. A plain cover signals "I didn't think about this." An atmospheric cover signals "the rest of the deck is going to reward your attention."

---

## 4. Pipeline with flow markers (hero slide)

When the centerpiece is a process, **show direction**, not just labels.

- Each stage is a numbered card (`stagenum` badge top-left, 24×24 circle with monospace number)
- Stages use **semantic colour progression** — cyan (ingestion) → green (interpretation) → purple (synthesis) → red (validation/rejection)
- Between stages: `▶` arrow connector with a thin grey baseline visible only between the dots — feels like a circuit trace
- Branches live in a separate lane below, with a rotated `分支` label on the left edge anchoring them visually
- Final output / business application is an `.ib` callout at the bottom

A reader sees "left-to-right pipeline + two branches feeding back in + final business application" before reading a single word. That's visual storytelling at work.

**See also.** [hero-patterns.md](hero-patterns.md) for the full pattern library (pipeline + 6 alternatives).

---

## 5. Number-dominant stat tiles

The figure dominates; the label whispers.

```
┌────────────────┐  ← corner accent stripe (4×32px)
│                │
│   46            │  ← 46px monospace number (left-aligned)
│                │
│   RECALL @ K=10 │  ← 11.5px uppercase mono label, 0.14em tracking
│   ↑ +12%        │  ← optional 11px delta indicator
└────────────────┘
```

- Number is 46px tabular-mono, ~3× the size of the label
- Label is uppercase, letter-spaced, the same family/weight as the eyebrow
- Optional `.unit` element for "%, ms, k" suffix
- Optional `.delta` element for trend indicators
- Five colour variants: `.stat`, `.stat-a`, `.stat-c`, `.stat-p`, `.stat-r`

**Why this matters.** Centred small numbers with equal-sized labels read as "list of metrics." Left-aligned dominant numbers read as "dashboard." The deck is making a quantitative argument — let the numbers be the argument.

---

## 6. Staggered entrance reveal

When a slide activates, content reveals in a brief cascade — *narrative pacing*, not animation for its own sake.

| Element | Delay | Duration | Easing |
|---------|------:|---------:|--------|
| `.shead` (the headline-and-proof block) | 0ms | 480ms | `cubic-bezier(0.16, 1, 0.3, 1)` |
| First content block after shead | 120ms | 540ms | same |
| Second content block | 200ms | same | same |
| Third content block | 280ms | same | same |
| **Cover special:** ornament line | 0ms | 600ms | width 0 → 64px |
| Cover: eyebrow | 200ms | 500ms | fade |
| Cover: h1 | 280ms | 700ms | up |
| Cover: lede | 420ms | 600ms | up |
| Cover: meta | 600ms | 500ms | fade |

The total cascade is under 700ms — fast enough not to delay, slow enough to direct the eye through the hierarchy: title → first idea → second idea.

**CSS-only.** No JavaScript-driven sequencing. Just keyframes on `.slide.active > *` with positional pseudo-selectors. Restart trick in the nav script: toggling `animationName = 'none'` then forcing a reflow then unsetting it.

**Print mode disables all entrance animation** — PDFs are deterministic, every element starts fully visible.

---

## Card system as ambient depth

Six card variants (default + 5 tints) all share a subtle elevation model:

- `box-shadow: inset 0 1px 0 rgba(255,255,255,0.02)` — a one-pixel inner highlight that gives dark cards a "lit from above" feel
- Tinted cards add `box-shadow: 0 0 0 1px rgba(accent,0.05)` — a 1px halo in the accent colour at 5% opacity, just enough to feel intentional
- On hover: `translateY(-1px) + box-shadow: 0 4px 18px rgba(accent,0.12)` — a soft ambient glow at 12% opacity, ~18px radius. Cards lift slightly, suggesting they're floating above the surface.

**Why this matters.** Flat cards on a flat dark background read as Bootstrap 2014. Subtle inner highlights + ambient accent glow reads as a designed system. The accent-tinted glow also reinforces semantic colour — hovering an amber card produces an amber glow, training the reader's eye on the colour system.

---

## Typography as voice

- **IBM Plex Sans + Noto Sans TC** — Plex has more character at small sizes (wider apertures, distinctive `Q` and `R`), feels more deliberate than Inter
- **IBM Plex Mono** for eyebrows, codes, badges, ratios — the same designer's hand
- **Weight contrast**: regular body (400) vs bold display (700) — no in-between weights (no 500 or 600 except in specific UI roles)
- **Negative tracking on display** (`letter-spacing: -0.02em`) — display feels sharper, more typographically aware
- **Generous tracking on small mono** (`letter-spacing: 0.12em+`) — small monospace labels read as labels, not text
- **Tabular figures everywhere** — numbers in tables and stat tiles align column-by-column

**The mix.** Sans body + mono eyebrows is a magazine convention. It signals "this is a designed publication, not a slide deck pulled out of a template."

---

## The negative space rule

The slide canvas has padding `46px 68px 62px`. Don't fight it. If the content doesn't fit, cut content — never reduce the padding to "make room."

**Why.** A slide with content right up to the edge reads as "I had too much to say." A slide with breathing room reads as "I cut the unnecessary, this is what matters."

If you must compress for a one-off slide, do so in the `#sN` per-slide override block at the bottom of `<style>`, **not** by changing the global `.slide` rule.

---

## When to break the system

The system is opinionated, but breaks are sometimes the right move:

- **Cover variant** — landscape photo cover with title overlaid? Fine for a project pitch, never for an RFC.
- **Full-bleed hero** — slide 5 could go full-bleed (no shead) if the diagram is so dominant it explains itself. Then the title moves into the diagram caption.
- **Black-page act break** — between major sections (e.g., before slide 11 references), insert a near-empty slide with a single sentence centered. Theatrical pause. Use sparingly.

If you break the system, **break it deliberately and only once per deck**. Two unrelated departures from the convention destroy the convention.
