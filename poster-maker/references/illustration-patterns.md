# Illustration Patterns Reference

When and how to add simple geometric SVG illustrations to a poster card.

The goal: **clarify relationships that text struggles with** — not decorate. If a sentence already conveys the structure, do not add an illustration. Every illustration must replace or strongly amplify text, never duplicate it.

---

## Decision Matrix — Trigger → Pattern

Scan card content for these structural signals. If a signal is present and the card has < 100 words after trimming, use the matched illustration.

| Content signal in card | Pattern | Why |
|------------------------|---------|-----|
| Central entity + 3–6 connections ("AI Agent connects to Slack, Email, Browser, Code") | **Hub & Spoke** | Shows fan-out at a glance; bullets list them flatly |
| Layered defence / trust / progressive depth ("perimeter → app → data → core") | **Concentric Rings** | Conveys "outer protects inner" — impossible in prose |
| Tech stack / dependency layering ("UI on API on DB on K8s") | **Stacked Layers** | Vertical order = semantic, list loses the spatial meaning |
| Two architectures or approaches compared, both with internal structure | **Comparison Boxes** | When `vs-layout` is just bullets, this adds structural diff |
| Narrowing stages with quantities ("10K leads → 800 trials → 120 paid") | **Funnel** | Width = quantity, immediate visual compression |
| 2D classification ("Risk × Impact = 4 quadrants") | **2×2 Matrix** | Pure spatial mapping — text equivalent is awkward |
| Sequential transformation with named transforms ("Raw → Clean → Featurized → Scored") | **Pipeline Boxes** | Different from `flow-strip` — when steps need internal labels |
| Set overlap (A, B, A∩B) | **Venn / Overlap** | Only when intersection is the point |

---

## Anti-Triggers — When NOT to Add an Illustration

Skip if any of these is true:

- Card body is already **≥ 100 words** of real content → illustration competes for space
- Card already uses 2+ visual components (`card-stat` + `data-bars`, etc.) → adding a third = noise
- Content is **purely numeric / single-metric** → use `donut-wrap`, `card-stat`, or `kpi-band` instead
- Content is **chronological sequence** → existing `timeline` component covers this
- Content is **flat bullet comparison** → existing `vs-layout` component covers this
- Template is **minimal** → that template wants typographic focus, not diagrams
- You cannot label every shape with real text from the input → illustration becomes meaningless ornament

**Single hardest test:** "If I delete this SVG, does a reader lose information that the prose doesn't recover?" — If no, do not add it.

---

## Sizing & Integration Rules

### Placement within a card

The illustration sits **between the card title (`h3`) and the bullets / paragraph**, OR replaces the bullets entirely when the diagram is the primary content.

```html
<div class="content-card c-cyan">
  <div class="icon-row">
    <div class="icon i-cyan">◈</div>
    <h3>Card title</h3>
  </div>
  <div class="poster-illus">
    <svg viewBox="0 0 400 200" ...>...</svg>
  </div>
  <p>One sentence framing what the diagram shows.</p>
  <!-- bullets optional — usually omit if diagram is the content -->
</div>
```

### CSS scaffold (add once to template)

```css
.poster-illus {
  width: 100%;
  display: flex;
  justify-content: center;
  padding: 8px 0;
}
.poster-illus svg {
  width: 100%;
  height: auto;
  max-height: 240px;   /* portrait card */
}
.poster-infographic-landscape .poster-illus svg {
  max-height: 200px;   /* landscape card — shorter */
}

/* Illustration text labels */
.illus-label {
  font-family: 'IBM Plex Mono', monospace;
  font-size: 14px;
  font-weight: 600;
  letter-spacing: 0.04em;
  fill: var(--tx);
}
.illus-label-sm {
  font-family: 'IBM Plex Mono', monospace;
  font-size: 12px;
  fill: var(--tm);
}
.illus-stroke { stroke: currentColor; fill: none; stroke-width: 1.5; }
.illus-fill   { fill: currentColor; }
.illus-faint  { stroke: var(--bd2); fill: var(--sf2); stroke-width: 1; }
```

### Style rules — keep it geometric, not illustrative

- **One accent colour per illustration** — inherit from card semantic colour via `color: var(--cyl)` then `stroke: currentColor`
- **Stroke weight: 1.5–2px** at viewBox scale — heavier reads as cartoon
- **No drop shadows, gradients, or filters** — flat geometry only
- **Text labels: 12–15px monospace** — must remain legible at 300 DPI print
- **Padding inside viewBox**: leave 12–16 units margin so strokes don't clip
- **Maximum 8 labelled elements** — past that the eye loses track

### Sizing budget per orientation

| Orientation | Max illus height | Card budget impact |
|-------------|-----------------|---------------------|
| Portrait card (1748px wide, 4 cards in 2×2) | 240px | ~30% of card vertical |
| Landscape card (3-col, 660px wide) | 200px | ~35% of card vertical |
| Landscape full-height card (`grid-row: span 2`) | 320px | ~25% of card vertical |

If the diagram doesn't fit, **simplify the diagram** (fewer nodes, shorter labels) — do NOT shrink labels below 12px.

---

## Pattern Library — Copy-Paste SVG Snippets

All snippets use `currentColor` so the active card's accent colour flows through. Replace `LABEL_*` tokens with real text from the input.

### 1. Hub & Spoke (radial)

For: "central concept + multiple integrations / channels / outputs". 3–6 spokes max.

```html
<svg viewBox="0 0 400 200" xmlns="http://www.w3.org/2000/svg" style="color: var(--cyl)">
  <!-- spokes -->
  <line class="illus-stroke" x1="200" y1="100" x2="60"  y2="40"  opacity="0.55"/>
  <line class="illus-stroke" x1="200" y1="100" x2="60"  y2="160" opacity="0.55"/>
  <line class="illus-stroke" x1="200" y1="100" x2="340" y2="40"  opacity="0.55"/>
  <line class="illus-stroke" x1="200" y1="100" x2="340" y2="160" opacity="0.55"/>
  <!-- outer nodes -->
  <circle class="illus-faint" cx="60"  cy="40"  r="22"/>
  <circle class="illus-faint" cx="60"  cy="160" r="22"/>
  <circle class="illus-faint" cx="340" cy="40"  r="22"/>
  <circle class="illus-faint" cx="340" cy="160" r="22"/>
  <text class="illus-label-sm" x="60"  y="44"  text-anchor="middle">LABEL_1</text>
  <text class="illus-label-sm" x="60"  y="164" text-anchor="middle">LABEL_2</text>
  <text class="illus-label-sm" x="340" y="44"  text-anchor="middle">LABEL_3</text>
  <text class="illus-label-sm" x="340" y="164" text-anchor="middle">LABEL_4</text>
  <!-- hub -->
  <circle cx="200" cy="100" r="38" fill="currentColor" opacity="0.18"/>
  <circle class="illus-stroke" cx="200" cy="100" r="38" stroke-width="2"/>
  <text class="illus-label" x="200" y="105" text-anchor="middle">HUB</text>
</svg>
```

### 2. Concentric Rings

For: "defence in depth", "trust layers", "core → perimeter". Up to 4 rings.

```html
<svg viewBox="0 0 400 200" xmlns="http://www.w3.org/2000/svg" style="color: var(--aml)">
  <circle class="illus-faint" cx="200" cy="100" r="90"/>
  <circle class="illus-faint" cx="200" cy="100" r="65"/>
  <circle class="illus-faint" cx="200" cy="100" r="40"/>
  <circle cx="200" cy="100" r="18" fill="currentColor" opacity="0.25"/>
  <circle class="illus-stroke" cx="200" cy="100" r="18" stroke-width="2"/>
  <!-- Layer labels float right of each ring -->
  <text class="illus-label-sm" x="296" y="104" text-anchor="start">LABEL_OUTER</text>
  <text class="illus-label-sm" x="271" y="104" text-anchor="start">LABEL_MID</text>
  <text class="illus-label-sm" x="246" y="104" text-anchor="start">LABEL_INNER</text>
  <text class="illus-label" x="200" y="105" text-anchor="middle">CORE</text>
</svg>
```

### 3. Stacked Layers

For: tech stack, abstraction layers, dependency tower.

```html
<svg viewBox="0 0 400 200" xmlns="http://www.w3.org/2000/svg" style="color: var(--gl)">
  <!-- bottom = foundation, top = surface -->
  <rect class="illus-faint" x="40" y="148" width="320" height="32" rx="2"/>
  <rect class="illus-faint" x="60" y="112" width="280" height="32" rx="2"/>
  <rect class="illus-faint" x="80" y="76"  width="240" height="32" rx="2"/>
  <rect x="100" y="40" width="200" height="32" rx="2" fill="currentColor" opacity="0.2"/>
  <rect class="illus-stroke" x="100" y="40" width="200" height="32" rx="2" stroke-width="2"/>
  <text class="illus-label-sm" x="200" y="168" text-anchor="middle">LABEL_L1</text>
  <text class="illus-label-sm" x="200" y="132" text-anchor="middle">LABEL_L2</text>
  <text class="illus-label-sm" x="200" y="96"  text-anchor="middle">LABEL_L3</text>
  <text class="illus-label"    x="200" y="60"  text-anchor="middle">LABEL_TOP</text>
</svg>
```

### 4. Pipeline Boxes

For: data/process pipeline with named transforms. 3–4 stages.

```html
<svg viewBox="0 0 400 120" xmlns="http://www.w3.org/2000/svg" style="color: var(--cyl)">
  <!-- 3 boxes -->
  <rect class="illus-faint" x="20"  y="40" width="100" height="40" rx="3"/>
  <rect class="illus-faint" x="150" y="40" width="100" height="40" rx="3"/>
  <rect x="280" y="40" width="100" height="40" rx="3" fill="currentColor" opacity="0.2"/>
  <rect class="illus-stroke" x="280" y="40" width="100" height="40" rx="3" stroke-width="2"/>
  <!-- arrows -->
  <path class="illus-stroke" d="M120 60 L150 60 M144 56 L150 60 L144 64" stroke-width="1.5"/>
  <path class="illus-stroke" d="M250 60 L280 60 M274 56 L280 60 L274 64" stroke-width="1.5"/>
  <text class="illus-label-sm" x="70"  y="64" text-anchor="middle">LABEL_1</text>
  <text class="illus-label-sm" x="200" y="64" text-anchor="middle">LABEL_2</text>
  <text class="illus-label"    x="330" y="64" text-anchor="middle">LABEL_3</text>
</svg>
```

### 5. Comparison Boxes (architecture diff)

For: "Approach A vs Approach B", both with internal structure to show.

```html
<svg viewBox="0 0 400 200" xmlns="http://www.w3.org/2000/svg" style="color: var(--cyl)">
  <!-- Left architecture -->
  <rect class="illus-faint" x="20" y="30" width="160" height="150" rx="4"/>
  <rect class="illus-faint" x="40" y="50" width="120" height="28" rx="2"/>
  <rect class="illus-faint" x="40" y="86" width="120" height="28" rx="2"/>
  <rect class="illus-faint" x="40" y="122" width="120" height="28" rx="2"/>
  <text class="illus-label-sm" x="100" y="68"  text-anchor="middle">LABEL_LA</text>
  <text class="illus-label-sm" x="100" y="104" text-anchor="middle">LABEL_LB</text>
  <text class="illus-label-sm" x="100" y="140" text-anchor="middle">LABEL_LC</text>
  <text class="illus-label" x="100" y="172" text-anchor="middle">CURRENT</text>
  <!-- Right architecture (accent) -->
  <rect x="220" y="30" width="160" height="150" rx="4" fill="currentColor" opacity="0.08"/>
  <rect class="illus-stroke" x="220" y="30" width="160" height="150" rx="4" stroke-width="2"/>
  <rect class="illus-stroke" x="240" y="50" width="120" height="28" rx="2" stroke-width="1.5"/>
  <rect class="illus-stroke" x="240" y="86" width="120" height="58" rx="2" stroke-width="1.5"/>
  <text class="illus-label-sm" x="300" y="68"  text-anchor="middle">LABEL_RA</text>
  <text class="illus-label-sm" x="300" y="120" text-anchor="middle">LABEL_RB</text>
  <text class="illus-label" x="300" y="172" text-anchor="middle">PROPOSED</text>
</svg>
```

### 6. Funnel

For: progressive narrowing with quantities. 3–4 stages.

```html
<svg viewBox="0 0 400 180" xmlns="http://www.w3.org/2000/svg" style="color: var(--pul)">
  <polygon class="illus-faint" points="40,20 360,20 320,60 80,60"/>
  <polygon class="illus-faint" points="80,70 320,70 280,110 120,110"/>
  <polygon points="120,120 280,120 240,170 160,170" fill="currentColor" opacity="0.25"/>
  <polygon class="illus-stroke" points="120,120 280,120 240,170 160,170" stroke-width="2"/>
  <text class="illus-label-sm" x="200" y="44"  text-anchor="middle">LABEL_TOP · N</text>
  <text class="illus-label-sm" x="200" y="94"  text-anchor="middle">LABEL_MID · N</text>
  <text class="illus-label"    x="200" y="150" text-anchor="middle">LABEL_BOT · N</text>
</svg>
```

### 7. 2×2 Matrix

For: classification on two axes (e.g., Impact × Effort).

```html
<svg viewBox="0 0 400 200" xmlns="http://www.w3.org/2000/svg" style="color: var(--gl)">
  <!-- axes -->
  <line class="illus-faint" x1="60" y1="20" x2="60" y2="180" stroke-width="1"/>
  <line class="illus-faint" x1="60" y1="180" x2="380" y2="180" stroke-width="1"/>
  <!-- quadrant boxes -->
  <rect class="illus-faint" x="70"  y="30"  width="150" height="70"/>
  <rect class="illus-faint" x="225" y="30"  width="150" height="70"/>
  <rect class="illus-faint" x="70"  y="105" width="150" height="70"/>
  <rect x="225" y="105" width="150" height="70" fill="currentColor" opacity="0.18"/>
  <rect class="illus-stroke" x="225" y="105" width="150" height="70" stroke-width="2"/>
  <!-- quadrant labels -->
  <text class="illus-label-sm" x="145" y="69"  text-anchor="middle">LABEL_TL</text>
  <text class="illus-label-sm" x="300" y="69"  text-anchor="middle">LABEL_TR</text>
  <text class="illus-label-sm" x="145" y="144" text-anchor="middle">LABEL_BL</text>
  <text class="illus-label"    x="300" y="144" text-anchor="middle">LABEL_BR</text>
  <!-- axis labels -->
  <text class="illus-label-sm" x="44" y="100" text-anchor="middle" transform="rotate(-90 44 100)">Y_AXIS</text>
  <text class="illus-label-sm" x="222" y="196" text-anchor="middle">X_AXIS</text>
</svg>
```

### 8. Venn Overlap

For: intersection of two concepts (only when the overlap itself is the point).

```html
<svg viewBox="0 0 400 200" xmlns="http://www.w3.org/2000/svg" style="color: var(--pul)">
  <circle class="illus-stroke" cx="150" cy="100" r="78" stroke-width="2" opacity="0.7"/>
  <circle class="illus-stroke" cx="250" cy="100" r="78" stroke-width="2" opacity="0.7"/>
  <!-- overlap region fill -->
  <path d="M 200,100 m -38,0 a 38,38 0 1,0 76,0 a 38,38 0 1,0 -76,0" fill="currentColor" opacity="0.2"/>
  <text class="illus-label" x="105" y="105" text-anchor="middle">SET_A</text>
  <text class="illus-label" x="295" y="105" text-anchor="middle">SET_B</text>
  <text class="illus-label-sm" x="200" y="105" text-anchor="middle">OVERLAP</text>
</svg>
```

---

## Workflow — Adding an Illustration to a Card

1. **After Step 3 content mapping**, scan each card's content for trigger phrases (Decision Matrix above).
2. If a card matches AND passes anti-trigger checks → pick the single best pattern.
3. Copy the SVG snippet into the card, between `.icon-row` and the next content element.
4. **Replace every `LABEL_*` token** with real text from the input — never leave placeholder tokens. Truncate labels to ≤ 14 chars; if a real concept can't fit, the diagram is wrong for this content.
5. Ensure `style="color: var(--<accent>l)"` matches the card's semantic colour (`--gl`, `--cyl`, `--aml`, `--pul`, `--rl`).
6. Trim bullets / paragraph if the diagram now duplicates them — usually the diagram replaces the bullets entirely; keep one framing sentence above or below.
7. Re-run overflow check after adding (`scrollH ≤ clientH + 2`).

---

## Quality Test — Before Shipping the Poster

For each illustration, all four must be true:

- [ ] **Information**: deleting the SVG loses information the prose does not recover
- [ ] **Labels**: every shape has a real text label from the input (no `LABEL_*` placeholders)
- [ ] **Restraint**: ≤ 8 labelled elements, ≤ 1 accent colour, strokes 1.5–2px
- [ ] **Fit**: poster passes overflow check unchanged

If any fails: remove the illustration. A poster with restrained typography beats one decorated with weak diagrams.
