---
name: poster-maker
description: |
  Generate publication-quality posters (portrait or landscape) from Markdown,
  documents, raw notes, or pasted text. Primary mode: infographic — data callouts,
  process flows, content cards — for business, conference, market analysis, product
  showcase, event announcement, research summary, or any single-page visual. Secondary
  mode: minimal — high-impact announcement or event one-pager. Output: self-contained
  HTML (open in Chrome → print → PDF for vector print quality) + optional Playwright
  PNG preview. Dark forensic theme (default) or light variant. Portrait (A0/A1/A2)
  or landscape. Extensible: academic multi-column templates archived at
  C:/Develop/poster/.archive/poster-maker-v1/templates/ if needed.
  NOT for multi-slide decks (use dark-deck-report).
---

# poster-maker

## When to invoke

Trigger on any of these:
- "做一張海報"、"make a poster"、"一頁式"、"infographic"、"展示板"
- User wants to turn a document / notes / data into a **single-page visual**
- Business one-pager: company overview, market summary, project status, product showcase
- Conference poster, event announcement, programme display
- Research / data summary that needs visual impact (non-multi-slide)

## When NOT to invoke

- Multi-slide decks → `dark-deck-report`
- Interactive web pages or dashboards
- When the user just wants formatted markdown output

---

## Templates (active)

| File | Use when |
|------|----------|
| `poster-infographic-portrait.html` | **Default.** Data, process, sections, business, research — any content-dense portrait poster |
| `poster-infographic-landscape.html` | Same as above but horizontal — conference display boards, wide-format |
| `poster-minimal.html` | Event, announcement, product launch — maximum visual impact, minimum copy |

**Extensibility note:** Academic 3-col and conference 4-col templates are archived at `C:/Develop/poster/.archive/poster-maker-v1/templates/`. Copy into `templates/` to re-activate.

---

## Reference file routing

Read a reference file only when you need it — do not pre-load all of them.

| Situation | Read this file |
|-----------|---------------|
| Export, screenshot, or print questions | `references/export-guide.md` |
| Font size, type scale, or readability decisions | `references/typography-scale.md` |
| Layout structure, grid, or zone questions | `references/layout-patterns.md` |
| Visual storytelling, narrative arc, LATCH | `references/visual-storytelling.md` |
| Geometric illustration (hub/rings/funnel/etc) — content benefits from a diagram | `references/illustration-patterns.md` |
| Choosing between poster types | `references/poster-types.md` |

---

## Non-Negotiable Rules

1. **No fabricated facts** — use only what's in the input. Mark inferences `[INFERRED]`
2. **Overflow = build error** — fixed canvas, `overflow: hidden` everywhere. Cut content before shrinking fonts
3. **Body text minimum: 26px CSS** (portrait) / **22px CSS** (landscape) at A1 scale (1–2m viewing distance). Both template defaults are already calibrated — do NOT reduce them to fit more content. Cut content instead. AI instinct to shrink fonts for fit creates posters that feel empty and unreadable when printed.
4. **Sparse upscale rule** — when a card has fewer than ~80 words of real content after trimming, scale UP rather than leaving visual void:
   - `--fs-section-h`: 36px → 42px
   - `--fs-body`: 26px → 30px
   - card `gap`: 22px → 28px
   Apply by overriding these variables inline on `.content-card.sparse` in a `<style>` block, or set them globally if ALL cards are sparse. Never scale down below the defaults.
5. **Semantic colour** — `--gl` green = positive, `--cyl` cyan = info/data, `--aml` amber = caution, `--pul` purple = synthesis, `--rl` red = problem. Not decorative
6. **Light theme for print** — business / public-facing posters often read better with `body.light`

---

## Workflow

### 1. Intake — extract structure from input

From the input (MD file, pasted text, bullet points), identify:

```
title        → main heading or first H1 (≤120 chars)
eyebrow      → category / event name / org (ALL CAPS, ≤40 chars)
hero_claim   → the core message in one sentence (for infographic hero zone)
stats[]      → up to 4 key numbers with labels (e.g. "94.7 / % Accuracy")
sections[]   → H2/H3 sections → content cards (up to 4)
flow[]       → sequential steps if process-oriented (3–5 steps)
cta          → call to action or conclusion
sources[]    → citations or data sources (1–4)
url          → for QR code placeholder
```

After extracting, compute a **density score**:
```
D = len(stats) × 2 + len(sections)
```
This drives template variant selection in Step 2.

### 2. Select template

**Orientation first:**
- User says "landscape" / "horizontal" → `poster-infographic-landscape.html` (max 3 cards — landscape has 3-col grid, not 4)
- Otherwise → portrait (see density routing below)

**Density routing (portrait):**

| D score | sections | Layout decision |
|---------|----------|-----------------|
| ≥ 7 | 4 | Full 2×2 infographic — **trim aggressively before filling** |
| 4–6 | 3–4 | Full 2×2 infographic — standard density |
| 3 | 2 | Infographic — **2-card variant** (add class `two-card` to `.content-grid`, remove bottom 2 card divs) |
| ≤ 2 | 1 | `poster-minimal.html` |
| 0 + event/announce focus | — | `poster-minimal.html` |

**Hero stat routing:**
- `len(stats) ≥ 1` → use `hero-stat-block` (pick most impactful stat for hero; remainder go to stats strip)
- `len(stats) = 0` → add class `no-stat` to `.hero`, remove `.hero-stat-block` div

Ask ONE question only if orientation is genuinely ambiguous:
> "Portrait (vertical, A1) or landscape (horizontal)?"

### 3. Content mapping

**Narrative arc — map content to poster thirds:**
- **HOOK** (top ~23%): HERO zone — make the viewer care in 5 seconds. One powerful claim + one dominant number.
- **BUILD** (middle ~60%): STATS STRIP + CONTENT CARDS — evidence, context, supporting data.
- **RESOLUTION** (bottom ~17%): FLOW STRIP + CTA + FOOTER — what to do, next steps, sources.

**LATCH organizing principle — choose one before mapping cards:**
- **Location**: geographic/regional comparison → use to order cards spatially
- **Alphabet**: glossary, named concepts → use for reference posters
- **Time**: sequence, before/after, roadmap → use FLOW STRIP as primary structure
- **Category**: grouping by type/theme → default for most business posters
- **Hierarchy**: ranking, priority, importance → put most impactful card top-left

**Slot mapping — fill these exactly (no guessing):**

*HERO zone:*
| Slot | Class | Limit | Note |
|------|-------|-------|------|
| 類別標籤 | `.hero-eyebrow` | ALL-CAPS · ≤ 40 chars | e.g. `MARKET ANALYSIS · 2026` |
| 主標題 | `.hero-title` | ≤ 90 chars | `<span class="accent">` 關鍵詞；`<br>` 只用於刻意換行 |
| 副標題 | `.hero-sub` | ≤ 140 chars | 一句話；**不加 `<br>`** |
| 作者/日期 | `.hero-meta` | ≤ 40 chars | `Org · YYYY-MM` |
| 主數字 | `.hero-stat-value` | ≤ 4 chars | 數字+單位；5–6 chars → 加 `class="long"`；無數字則移除整個 `.hero-stat-block` |
| 指標名稱 | `.hero-stat-label` | ALL-CAPS · ≤ 20 chars | — |
| 指標說明 | `.hero-stat-desc` | ≤ 60 chars | — |

*STATS STRIP (per cell):*
| Slot | Limit | Note |
|------|-------|------|
| `.sv` 數值 | ≤ 8 chars | 純數字+單位；若 < 2 個 stat 則移除整個 `.stats-strip` |
| `.sl` 標籤 | ALL-CAPS · ≤ 4 words | — |
| `.sd` 說明 | ≤ 20 chars | 可省略 |

*CONTENT CARD (per card):*
| Slot | Limit | Note |
|------|-------|------|
| `.icon` | 單一 Unicode 字元 | `▶ ◈ ◆ ✦ ⬡ ◉ ▲ ✓` |
| `h3` 標題 | ≤ 30 chars | — |
| `p` 內文 | 40–70 words | **不加 `<br>`**；中文約 80–140 字 |
| `ul li` 條列 | ≤ 12 words/條 · 最多 3 條 | **不加 `<br>`** |

*FLOW STRIP (per step):*
| Slot | Limit |
|------|-------|
| `strong` 步驟名 | ≤ 20 chars |
| `span` 說明 | ≤ 30 chars |

*FOOTER:*
- 來源條目 `.footer-ref`：≤ 80 chars；最多 4 條
- QR `.qr-url`：填入實際 URL 或 doi（不是佔位符）

**Zone presence rules:**
- `STATS STRIP`: remove if < 2 stats
- `FLOW STRIP`: remove if no sequential process
- `CTA ZONE`: remove if no call-to-action
- `hero-stat-block`: remove if no dominant numeric stat

**Priority triage — label before trimming:**
- **P0** (never cut): hero_claim, primary stat (hero stat block), section titles
- **P1** (cut last): card bullets (max 3), stats strip values, flow step labels
- **P2** (cut first): card body paragraphs, flow step sub-descriptions, extra sources

**Capacity constraint:** A1 portrait holds ~900 words across all zones at legible size. If total intake exceeds this, apply trim rules immediately:
1. Convert P2 paragraphs → bullets or delete (saves ~30%)
2. Cut supporting examples, keep conclusions
3. Cut to 3 sources max
4. Last resort: cut a content card entirely (better than shrinking text)

**Sparse rule:** If a card has < 80 words of real content after trimming, add class `sparse` to `.content-card`.

**Visual elements toolkit — select per card based on content type:**

| 元件 | 使用時機 | 操作 |
|------|----------|------|
| `.card-stat` | 此卡片有 1 個主要指標 → 以大字展示 | 取消注解 card-stat 區塊 |
| `.data-bars` | 2–4 項目需量化比較 → 取代條列 | 取消注解 data-bars；設各行 `--w` = 實際比例 |
| `.callout` | 1 個關鍵洞察需強調，≤30 字 | 取消注解 callout |
| `.donut-wrap` | 單一百分比或完成率 → 圓環圖 | 取消注解 donut-wrap；設 `--pct`（0–100）、`--clr` |
| `.vs-layout` | 兩個選項/方案對比 → 左右分欄 | 取消注解 vs-layout；填入兩側內容 |
| `.timeline` | 時間軸/里程碑/前後順序 → 垂直脊柱 | 取消注解 timeline；每個節點填 `<strong>` 時間 + `<span>` 說明 |
| `.pullquote` | 重要引言或定義性陳述 | 取消注解 pullquote；用 `<blockquote>` + `<cite>` |
| `<span class="kpi">` | 段落中有關鍵數字需突出 | 直接包裹數字（`.kpi.cyan/.amber/.red`） |
| `.sv-bar` `.sv-bar-fill` | Stats strip 量化數值的比例條 | 設 `--w:` = 實際百分比；若數值是類別型（非量化）→ 加 `class="no-bar"` 到 `.stat-cell` 隱藏比例條 |

**選用規則：**
- 有量化數據時，每張卡片至少使用 1 個視覺元件
- 同一張卡片最多 2 種元件；`.card-stat` 與 `.data-bars` 互斥（擇一）
- `.callout` 可與任一元件並用，放在段落或條列之後
- 無量化數據的卡片：用 `.callout`、`.pullquote` 或 `<span class="kpi">` 突出最重要的一句話
- `.vs-layout` 或 `.timeline` 使用時通常取代整個 `<ul>` 條列區塊

**Illustration decision — when content needs a geometric SVG diagram:**

After the toolkit selection above, scan each card for **structural relationships that text expresses awkwardly**. If a card matches a trigger, an inline SVG illustration replaces or strongly amplifies its content.

| Card content signal | Pattern | Trigger if card has |
|---------------------|---------|---------------------|
| Central entity + 3–6 outward connections | Hub & Spoke | "connects to / integrates with / across" + named items |
| Layered defence or progressive depth | Concentric Rings | "perimeter / layers / core / defence in depth" |
| Tech stack / dependency tower | Stacked Layers | "built on / stack / layers of" + named tiers |
| Two architectures with internal structure | Comparison Boxes | "before/after, A vs B" where each side has parts |
| Narrowing stages with quantities | Funnel | sequence with decreasing numbers |
| 2D classification | 2×2 Matrix | "X-axis × Y-axis = quadrants" |
| Pipeline with named transforms | Pipeline Boxes | "A → B → C" with stage labels (use this not flow-strip when steps need internal sub-labels) |
| Set intersection where overlap is the point | Venn Overlap | "A ∩ B is where X happens" |

**Skip illustration if any:**
- Card already has ≥ 100 words of real content after trimming
- Card already uses 2+ visual components from the toolkit
- Content is purely numeric → use `donut-wrap` / `card-stat` / `kpi-band` instead
- Content is chronological → use `timeline` instead
- Content is flat bullet comparison → use `vs-layout` instead
- Template is `poster-minimal.html` (typography-focused, no diagrams)
- You cannot fill every shape label with real text from the input

**Single test:** delete the diagram in your head — does the reader lose information the prose does not recover? If no, do not add it.

**When adding:** read `references/illustration-patterns.md` for copy-paste SVG snippets, sizing rules (max 240px tall portrait / 200px landscape), and the required CSS scaffold (`.poster-illus`, `.illus-stroke`, `.illus-label`). Inherit the card's semantic accent via `style="color: var(--cyl)"` etc. Replace every `LABEL_*` token with real text from the input — never leave placeholder tokens.

**顏色配對 — 語義優先，位置其次：**

顏色應傳達語義，而非位置。先問「這張卡片的內容性質是什麼？」再選色：

| 語義 | 顏色 | 用法 |
|------|------|------|
| 優勢、成功、正向結果 | `c-green` / `i-green` | `.kpi`（預設綠） |
| 數據、資訊、中性事實 | `c-cyan` / `i-cyan` | `.kpi.cyan` |
| 警告、成本、挑戰 | `c-amber` / `i-amber` | `.kpi.amber` |
| 洞察、合成、複雜關聯 | `c-purple` / `i-purple` | — |
| 問題、風險、劣勢 | `c-red` / `i-red` | `.kpi.red` |

**如何套用語義色：**
1. 在 `.content-card` 加 class：`<div class="content-card c-amber">` — 自動覆蓋 accent strip、card glow、callout 邊線、card-stat 數字、data-bar 填色
2. 同一張卡片的 icon 必須用對應 class：`i-amber`
3. **當語義明確時，不受位置約束**：第1張卡可以是紅色（風險），第4張可以是綠色（結論）

**預設（無明確語義）：** 仍可使用位置配色（Card 1 = green, 2 = cyan, 3 = amber, 4 = purple），此時不需加 `c-*` class。

**語義顏色限制：** 5張卡最多用 3 種顏色；相鄰卡片避免使用相同顏色。

### 4. Fill and write HTML

**Fill order — hero last:**
1. Fill stats strip cells
2. Fill content cards (all 4 or fewer)
3. Fill flow strip and CTA if present
4. Fill footer sources
5. **Fill hero zone last** — hero_claim should synthesize across all cards; write it after understanding the full content

- Replace every `{{PLACEHOLDER}}` token
- Remove entire `<!-- optional -->` sections that have no content
- For icons, use single Unicode chars: `▶` `◈` `◆` `✦` `⬡` `◉` `⬛` `▲` `✓`
- If content has a URL/DOI: leave QR placeholder with the URL as `.qr-url` text
- Apply `body.light` class if: user requested light theme, or content is for public/print use

**繁體中文內容規則：**
- **`<br>` 只用於 `.hero-title` 和 `.main-headline`** 的刻意換行；body / bullet / subtitle **絕對不加 `<br>`**，讓 CSS 自然換行
- **中英混排**：英文數字自然嵌入中文句中，不需用空格包圍（CSS `word-break: break-word` 處理）
- **標點符號**：不在句中強制換行；CSS `line-break: strict` 已防止標點出現行首
- **編碼**：Write tool 預設 UTF-8 ✓；`<meta charset="UTF-8">` 必須是 `<head>` 的第一個 meta tag
- **字型離線**：Google Fonts CDN 需網路；若離線環境，字型降格為系統黑體（版面不壞但字型不同）

**Scan before writing:** grep for `{{` — zero remaining placeholders before writing the file.

### 5. Write output

```
./output/poster-{slug}-{YYYYMMDD}.html
```
`slug` = first 3–4 words of title, kebab ASCII lowercase.

### 6. Deliver

**Default — HTML only (always do this):**

The HTML file is the primary deliverable. Tell the user:
> Open `output/poster-{slug}-{YYYYMMDD}.html` in Chrome → Ctrl+P → Paper: A1 → Margins: None → Background graphics: ✓ → Save as PDF

This is vector output at any DPI — superior to PNG for printing.

**Optional — Playwright PNG preview (only if user explicitly asks for a screenshot):**

```bash
# Start HTTP server (Playwright blocks file://)
C:\Programs\miniforge3\envs\deve\python.exe -m http.server 8743
# (run from ./output/ directory)
```

```
browser_navigate(http://localhost:8743/poster-{slug}-{YYYYMMDD}.html)
browser_resize(1748, 2480)   # A1 portrait; landscape: (2480, 1748)
browser_wait_for(time: 2)
browser_take_screenshot(fullPage: false, filename: ./output/poster-{slug}.png)
```

See `references/export-guide.md` for Playwright PDF, Chrome DevTools, and multi-format export details.

### 7. Output checklist

- [ ] Zero `{{` tokens remain
- [ ] Zero `LABEL_*` tokens remain in any inline SVG illustration
- [ ] Header has eyebrow + hero title
- [ ] Footer has at least one source line
- [ ] Background is not default white (dark theme) or not pure black (light theme)
- [ ] Optional sections removed if no content (flow strip, CTA zone, stats strip)
- [ ] Each inline SVG illustration has real labels, ≤ 8 shapes, single accent colour, passes "loses information if deleted" test

---

## Canvas dimensions

| Size | Portrait (W×H px) | Landscape (W×H px) |
|------|-------------------|-------------------|
| A2 | 1240×1754 | 1754×1240 |
| **A1 (default)** | **1748×2480** | **2480×1748** |
| A0 | 2480×3508 | 3508×2480 |

Set on `.poster { width: Xpx; height: Ypx; }` — do not use `100vw/100vh` for posters (breaks Playwright sizing).

---

## Design tokens (quick reference)

```css
/* Dark (default) */                /* Light (body.light) */
--bg:  #060d07                      --bg:  #f8f5f0
--sf:  #0c1a0e                      --sf:  #edeae4
--tx:  #e8f5ea                      --tx:  #1a1a14
--tm:  #9fbfa6                      --tm:  #4a4a3a
--gl:  #4ade80   (green)            --gl:  #16a34a
--cyl: #22d3ee   (cyan)             --cyl: #0891b2
--aml: #fbbf24   (amber)            --aml: #d97706
--pul: #a78bfa   (purple)           --pul: #7c3aed
--rl:  #f87171   (red)              --rl:  #dc2626
```

Fonts: `'IBM Plex Sans', 'Noto Sans TC', sans-serif` + `'IBM Plex Mono'` for labels/stats.

---

## Extensibility

To add a new template type:
1. Copy the closest existing template from `templates/` or from `.archive/poster-maker-v1/templates/`
2. Adjust canvas size, grid layout, section zones
3. Add a row to the Templates table above with the trigger condition
4. No other files need changing — SKILL.md is the single source of truth
