# Layout Patterns Reference

CSS Grid and Flexbox recipes for the active infographic templates.

---

## Infographic Portrait — Core Structure

**Canvas:** 1748×2480 CSS px

```
.poster {
  width: 1748px; height: 2480px;
  display: flex; flex-direction: column;
  overflow: hidden; box-sizing: border-box;
}
```

The poster is a vertical flex column. Sections stack top-to-bottom:

```
HERO         flex-shrink: 0; height: ~560px  (22.6% of canvas — 5-second rule threshold)
STATS STRIP  flex-shrink: 0; height: 180px
CARDS        flex: 1  (takes remaining space)
FLOW STRIP   flex-shrink: 0; ~160px  (optional)
CTA ZONE     flex-shrink: 0; ~120px  (optional)
FOOTER       flex-shrink: 0; ~90px
```

**Cards 2×2 grid:**
```css
.content-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  /* Borders between cards — no gap, use border-right/border-top */
}
```

**Content density variants (add CSS class + adjust HTML):**

| Situation | Action |
|-----------|--------|
| 4 sections (D ≥ 4) | Default 2×2, no change |
| 2 sections (D = 3) | Add `class="two-card"` to `.content-grid` + remove bottom 2 card `<div>`s |
| 1 section (D ≤ 2) | Switch to `poster-minimal.html` |
| No key metric | Add `class="no-stat"` to `.hero` + remove `.hero-stat-block` div |
| Sparse card body (< 20 words) | Add `class="sparse"` to `.content-card` — centers content, increases line-height |

---

## Infographic Landscape — Core Structure

**Canvas:** 2480×1748 CSS px

```
.poster {
  width: 2480px; height: 1748px;
  display: grid;
  grid-template-columns: 680px 1fr;  /* left hero | right content */
  grid-template-rows: 1fr auto;
  overflow: hidden;
}
```

Left column (hero) spans full height via `grid-row: 1 / 3`.
Right side is a flex column: stats strip → cards → flow → (footer is its own grid cell).

**Cards 3-col (landscape):**
```css
.cards-area {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  /* border-right between cards */
}
```

---

## Hero Zone

```css
/* Portrait: row layout — text left, dominant stat right */
.hero { height: 560px; /* 22.6% of 2480px — 5-second rule */ }
.hero-text { flex: 1; padding-right: 48px; }
.hero-stat-block { width: 320px; border-left: 1px solid var(--bd2); padding-left: 48px; }
.hero-stat-value { font-size: 96px; font-weight: 700; color: var(--gl); }

/* OR left column for landscape */
.hero-col { width: 680px; /* 27.4% of 2480px */ }

/* Background glow — always: */
background-image:
  radial-gradient(ellipse 80% 120% at 80% 50%, rgba(74,222,128,0.12), transparent 70%),
  radial-gradient(ellipse 60% 80% at 10% 50%, rgba(34,211,238,0.08), transparent 60%);
```

Hero always has: eyebrow (monospace ALL-CAPS + dot) → big title → subtitle → org/date at bottom.
Portrait hero also has a **dominant stat block** on the right (remove if no key number).

**Title max lines:**
- Portrait: 2 lines at 88px (max-width 1000px, within ~1140px hero-text area)
- Landscape hero col: 3 lines at 72px

**Hero stat sizing:**
- Portrait: 96px value, 17px label
- Landscape: 80px value (inside `.hero-stat` card), 14px label

---

## Stats Strip

```css
.stats-strip {
  display: grid;
  grid-template-columns: repeat(N, 1fr);  /* N = number of stats */
  /* Each cell: flex column, center aligned, border-right */
}
```

**When to remove stats strip:**
- Fewer than 2 stats → remove entire `<div class="stats-strip">` from HTML
- Never render empty stat cells — remove the cell, adjust grid-template-columns

**Stat value font size:** 76px portrait, 64px landscape. Never smaller.

---

## Content Cards

```css
/* Each card */
.content-card {
  padding: 32px 40px;  /* portrait */  /* 24px 28px landscape */
  display: flex;
  flex-direction: column;
  gap: 14px;
  overflow: hidden;
}
```

**Icon variants (use Unicode, not images):**
```
▶ ◈ ◆ ✦ ⬡ ◉ ▲ ✓ ★ ◎ ⬛ ❯
```
Each icon gets a colored background tile matching the card's accent:
```css
.icon.i-green  { background: var(--bg-g); border: 1px solid var(--gl);  color: var(--gl); }
.icon.i-cyan   { background: var(--bg-c); border: 1px solid var(--cyl); color: var(--cyl); }
.icon.i-amber  { background: var(--bg-a); border: 1px solid var(--aml); color: var(--aml); }
.icon.i-purple { background: var(--bg-p); border: 1px solid var(--pul); color: var(--pul); }
```

**Bullet arrow colour follows card order:**
- Card 1 → `var(--gl)` green
- Card 2 → `var(--cyl)` cyan
- Card 3 → `var(--aml)` amber
- Card 4 → `var(--pul)` purple

---

## Flow Strip

```css
.flow-steps { display: flex; align-items: center; gap: 0; }
.flow-step { flex: 1; /* each step takes equal width */ }
.flow-arrow { flex-shrink: 0; color: var(--td); font-size: 22px; }
```

Max 5 steps in portrait, 4 steps in landscape (space constraint).
If > 5 steps: group into phases and label them.

**Step number colour rotation:**
```css
:nth-child(3n+1) → var(--gl)   green
:nth-child(3n+2) → var(--cyl)  cyan
:nth-child(3n+3) → var(--pul)  purple
```

---

## CTA Zone (Portrait Only)

```css
.cta-zone {
  padding: 28px 56px;
  background: var(--bg-g);  /* subtle green tint */
  display: flex;
  align-items: center;
  justify-content: space-between;
}
```

Contains: left text (headline + sub) + right badge button.
Remove entire block if no call to action exists.

---

## Footer

```css
.footer {
  display: grid;
  grid-template-columns: auto 1fr auto;
  /* label | refs | QR block */
  padding: 16px 56px;  /* portrait */ /* 12px 28px landscape */
  border-top: 1px solid var(--bd);
}
```

QR box: 88×88px portrait, 64×64px landscape. Always shown even if just a text URL fallback.

---

## Overflow Safety

Every container: `overflow: hidden`.
Body sections use `flex-shrink: 0` so they don't collapse. Only CARDS gets `flex: 1`.

**If content overflows at render time:**
1. Reduce card `padding` from 32→20px
2. Reduce `gap` from 14→8px
3. Trim body text (convert paragraph → bullets)
4. Remove one card entirely

Never reduce font sizes — that violates the minimum readability rule.

---

## Minimal Template Structure

```
.poster {
  display: grid;
  grid-template-rows: auto auto auto auto 1fr auto auto;
  /* top-bar | headline-block | visual-zone | points | event-strip(opt) | footer */
}
.points-grid { grid-template-columns: 1fr 1fr 1fr; }
```

Points always exactly 3 — colour accent top border per card (green / cyan / purple).
