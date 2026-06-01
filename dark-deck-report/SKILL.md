---
name: dark-deck-report
description: |
  Build dense, defensible, citation-rigorous report decks as self-contained HTML
  files for forensic-analytical genres: investigation / audit / postmortem,
  technical RFC / engineering brief, strategy consulting deck, research summary,
  market / industry analysis. McKinsey conclusion-first structure, dark forensic
  aesthetic (default) with a matching light inversion (warm white + ink-green)
  for print or bright-room delivery. Print-clean A4-landscape PDF, fixed 16:9
  canvas where overflow is a build error. Three depths (14-slide full / 4-slide
  brief / 1-slide memo) × two themes. NOT for pitch decks, brand / marketing
  decks (use `bright-pitch-deck` when available), or regulatory / clinical
  documents (use `plain-document-report` when available) — the aesthetic is
  wrong by design.
---

# Dark Deck Report

A repeatable pipeline for **dense, defensible, print-ready** report decks delivered as a single HTML file. Survives projection, exports cleanly to PDF, carries verifiable evidence, never looks AI-generated.

The skill is opinionated about *execution discipline* (typography, overflow, citation precision, print contract) and *aesthetic identity* (dark forensic palette, semantic accents, staggered reveal). It is **agnostic** about subject matter — the same chassis serves an ML architecture brief, a security postmortem, a quarterly research summary, or a product strategy doc.

---

## Conformance language

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL** in this document are to be interpreted as described in RFC 2119 and RFC 8174 — **and only when they appear in ALL CAPITALS.** A lowercase "must" / "should" / "may" is ordinary prose, not a normative requirement.

Each level maps to a concrete runtime consequence:

| Level | Meaning | Consequence if unmet |
|---|---|---|
| **MUST** / **MUST NOT** / **REQUIRED** | Absolute requirement / prohibition | **BUILD ERROR** — stop; do not deliver the deck |
| **SHOULD** / **SHOULD NOT** / **RECOMMENDED** | Strong default; deviating REQUIRES a stated reason | **WARN** — surface the deviation to the user, then MAY proceed |
| **MAY** / **OPTIONAL** | Genuine free choice | none |

**Assigning BLOCK vs WARN (Design-by-Contract lens).** When deciding a new rule's level, attribute the fault: a **precondition** failure (missing or invalid *input* the skill depends on) is the caller's fault — refuse or ask the user rather than guessing. A **postcondition** failure (the skill's *own output* violates a rule) is the skill's fault — BLOCK and fix before delivery; never ship it behind a warning. An **invariant** (must hold throughout, e.g. output stays self-contained HTML that survives the overflow contract) is always a `MUST`.

This vocabulary is shared verbatim with `report-synthesis` (the upstream synthesis skill) and `poster-maker`, so a `MUST` carries the same weight across the pipeline.

---

## When to invoke

Use this skill when the user asks for:

- 簡報式網頁 / 投影片網頁 / report deck / executive briefing / technical brief
- A report that **must export to PDF cleanly** (one slide → one A4 landscape page)
- A document needing **verifiable citations** (papers, vendors, standards, datasets)
- A **dense single-file deliverable** the user can email, version, or pass through review
- Common subjects: investigation / audit / postmortem, technical RFC / engineering brief, strategy consulting deck, research summary, market / industry analysis, architecture brief, due-diligence memo, design review

Do NOT use for:
- **Startup pitch decks or brand / marketing decks** — the forensic chassis kills the emotional energy these genres need. Wait for a future `bright-pitch-deck` skill.
- **Regulatory / clinical / legal submissions** — these need A4 portrait + numbered hierarchical sections (ICH/CTD style), not a 16:9 deck. Wait for a future `plain-document-report` skill.
- **Earnings / financial decks** — semantic-color collision (red=loss in finance vs red=reject here); brand-locked palettes are usually required anyway.
- Marketing landing pages, blog posts, dashboards, scrolling long-form sites, anything where the reader controls the scroll.

---

## Input modes

Two modes, picked automatically by presence/absence of `outline.md` in the working directory (or user-named subdirectory).

### Mode A — consume `outline.md` (preferred for multi-source reports)

If `outline.md` is present, it was produced by the `report-synthesis` skill and is the authoritative source of content + structure. Parse its YAML frontmatter and the per-slide YAML fenced blocks; render the deck from those fields.

**Validation contract**: checks 1–5 are `MUST` (a failure is a **BUILD ERROR** — refuse render). Checks 6–9 are this renderer's additional handling.

*Schema-mandated checks (per [schema-v0.1.md §12](../report-synthesis/references/schema-v0.1.md) — every renderer enforces these)*:

1. You **MUST** parse `schema_version` (supported: `0.1`) and **MUST** fail loudly on a major-version mismatch with a migration message.
2. You **MUST** verify `dark-deck-report` appears in `compatible_renderers`; if not, you **MUST** refuse and name the correct render skill.
3. You **MUST** verify `report_type ∈ { investigation, audit, postmortem, strategy }` (v0.2 adds `rfc`, `research`, `market-analysis`). On miss, you **MUST** refuse with a pointer to the future render skill.
4. You **MUST** run placeholder grep on `outline.md` BEFORE rendering: `grep -iE "lorem|xxxx|tbd|todo|fixme|\[ai-inferred\]"`. Any hit **MUST** refuse render with a file:line list.
5. You **MUST** run the ghost-deck test on rendered slide titles in order; an incoherent narrative **MUST** block delivery.

*Dark-deck-specific extras (this renderer's additional handling)*:

6. You **MUST** honour `delivery.depth` (full / brief / minimal) and `delivery.theme` (dark / light) → select the `deck-{depth}{-light}?.html` chassis.
7. You **MUST** reject `color_semantics: finance` — the semantic-color collision is not supported here; finance work waits for a dedicated render skill.
8. You **MUST** honour `narrative_template` to pick slide-arc emphasis (per §Report-type variations).
9. You **SHOULD** honour `hero_pattern` per §Hero-slide patterns; for v0.2 patterns not yet implemented (`quadrant-bubble`, `evidence-grid`) you **MUST** fall back per [schema §9](../report-synthesis/references/schema-v0.1.md) and **WARN** the user.

**Field-to-slot mapping**:

| `outline.md` field | Rendered slot |
|---|---|
| `title` | Cover h1 |
| `governing_thought.resolution` | Cover sub-line / TL;DR opener |
| Slide `action_title` | Slide h2 |
| Slide `bullets` | Slide body cards or `<ul>` |
| Slide `evidence_refs` | Footnote markers; `evidence.md` entries → References slide (`role: evidence` — #11 in full, #4 in brief) |
| Slide `hero_pattern` | Hero slide layout choice (see [references/hero-patterns.md](references/hero-patterns.md) catalogue) |
| Slide `data_callouts` | Numeric overlay on hero / stat tiles |
| `citation_register` | Reference row format (per §Citation discipline) |
| `delivery.depth` | Chassis variant (full=14 / brief=4 / minimal=1) |
| `delivery.theme` | Chassis variant (dark default / light inversion) |

**Anti-fabrication**: Mode A inherits the Core Law from `report-synthesis`. You **MUST NOT** add bullets, numbers, or citations that aren't in `outline.md` / `evidence.md`. If a slot is empty, you **MUST** leave it empty or surface it to the user — you **MUST NOT** invent content to fill it.

### Mode B — direct invocation (ad-hoc)

If no `outline.md` is present, operate as the skill always has: the user provides content fragments inline, the LLM drafts directly into the chassis. The Non-negotiable principles below still apply.

Use Mode B for: single-author decks with inline content; quick prototyping; 1-slide memos from a clear paragraph. Use Mode A for: multi-source reports; external review; anything where citation discipline matters.

---

## Non-negotiable principles

These are the load-bearing invariants of the chassis. Each is stated at its normative level; a `MUST`/`MUST NOT` here is a **build error** when violated.

1. **Headline IS the conclusion (McKinsey pyramid).** Every slide title **MUST** be a complete sentence stating the takeaway, and its sub-title **MUST** carry the proof (numbers, citation, scope). Titles that read like topics ("Architecture Overview", "Methodology") are unfinished work and **MUST** be rewritten. Register **MUST** be declarative-factual, not persuasive-prescriptive — see [`report-synthesis/references/neutral-language.md`](../report-synthesis/references/neutral-language.md) for the zh/en replacement tables. "保險業面臨壓力" → "保險業適用個資法第6條"; "we must adopt X" → "regulation Y requires X by date Z". A forensic-analytical deck that reads as advocacy gets dismissed.
2. **Dark forensic aesthetic.** Background `#060d07`, primary text `#e8f5ea`. The five accent colours **MUST** keep their fixed semantic meanings — see [references/visual-storytelling.md](references/visual-storytelling.md). Reusing an accent for a meaning it does not own (e.g. amber for "info") is a defect, not a style choice.
3. **Fixed-canvas, not fluid.** Slides are designed for a 1707×960 canvas (16:9) and measured at that size by the overflow script — CSS uses `100vw/100vh`, so the literal pixels are enforced at verification time. Content **MUST** fit; any overflow is a **build error**. See §Overflow workflow.
4. **Print-PDF first-class.** A4 landscape, one slide per page, zero margin loss. The `@media print` block is load-bearing — you **MUST NOT** modify it without re-running the print test (§Print contract).
5. **References are venue-precise.** Every reference **MUST** be cited as `Paper / Product Name (Venue YEAR · Institution · stable-id)`. A bare URL is **NOT** an acceptable citation. See §Citation discipline.
6. **Open-source neutrality.** You **SHOULD** prefer Apache 2.0 / MIT / BSD tooling. You **MUST NOT** write exclusion lists — simply omit excluded items and keep framing neutral.
7. **Presentation-readable typography.** Body 18px, h2 42px, table cells 17px (brief/minimal baselines differ — see §Typography baselines). The deck is projected, so you **MUST NOT** reduce any size below its published floor to "fit more text" — cut the text instead.
8. **Vertical fill.** Every non-cover slide **MUST** use `justify-content: space-between` so content distributes across the full canvas; a top-heavy slide with an empty lower half is a **build error**. The cover slide **MUST** override with `justify-content: center`.

---

## Canonical structure

Three template variants (dark forensic by default) + matching light inversions:

```
templates/
├── deck-full.html             ← 14-slide chassis (cover → arc → close)
├── deck-full-light.html       ← same chassis, inverted theme
├── deck-brief.html            ← 4-slide executive condensation
├── deck-brief-light.html      ← brief, inverted theme
├── deck-minimal.html          ← 1-slide standalone (one-page memo)
└── deck-minimal-light.html    ← memo, inverted theme
```

**Dark is the default.** Pick light only when the delivery context demands it — printed handouts where toner cost matters, audiences who reject dark UI, projection in brightly-lit rooms where white surfaces wash out less than dark, or government / clinical settings where dark-mode reads as informal. Every light variant has **structural parity** with its dark counterpart (byte-identical layout primitives, fonts, overflow contract, and print block); only `:root` tokens and a handful of color-direction details (card shadows, cover blooms, nav chrome) differ. Structural parity is locked at write-time; **browser-render verification of the light variants is still pending** — see §Template state. See §Light variant token map for the swap recipe.

### Universal 14-slide arc

The roles below are **abstract** — each report type emphasises different slides. The `Role` column matches the `role` enum in [schema §7](../report-synthesis/references/schema-v0.1.md) so synthesis-emitted slides bind to the correct chassis slot. See [references/report-types.md](references/report-types.md) for which roles map to your subject.

| # | `role` (schema §7) | Display name | What it answers |
|---|---|---|---|
| 1 | `cover` | Cover | Title, thesis-as-sentence, audience, date, version |
| 2 | `tldr` | TL;DR | What does this doc argue, in 3–5 bullets? |
| 3 | `context` | Problem / Context | What's broken, what's the gap, why does it matter — quantified |
| 4 | `thesis` | Thesis / Proposal | The one-sentence answer + why this approach over alternatives |
| 5 | `hero` | **Hero slide** | The centerpiece visual; layout chosen per `hero_pattern` |
| 6 | `detail` | Detail A | First deep-dive — early stage, contributing factor, sub-theme |
| 7 | `detail` | Detail B | Second deep-dive — core mechanism, primary finding |
| 8 | `branch` | Branch / Specialty A | Side-pipeline, sub-domain, alternative angle |
| 9 | `branch` | Branch / Specialty B | Second side-pipeline or counterpoint |
| 10 | `comparison` or `risk` | Quantitative model / Tradeoff | Formulas, thresholds, scoring, ROI math, KPI definitions |
| 11 | `evidence` | References | Numbered, venue-precise, dense (rendered from `evidence.md`) |
| 12 | `comparison` | Selection matrix | Tools / vendors / approaches with criteria + priority |
| 13 | `timeline` or `recommendation` | Roadmap / Plan | Phased delivery, gates, dates, owners |
| 14 | `recommendation` or `appendix` | Closing | Decisions requested, owners, next actions, contact |

Slides with the same `role` value (e.g., two `detail` slides) are positioned by emit-order within the chassis. When a `role` is missing from `outline.md`, the renderer either drops the slide (for `optional` roles) or surfaces a warning (for `required` roles per the narrative template).

### Brief deck arc (4 slides)

Strict subset of the full deck. Brief = condensation, never new content.

| # | Role |
|---|------|
| 1 | Problem + thesis combined |
| 2 | Hero slide (architecture / matrix / timeline) |
| 3 | Selection matrix condensed |
| 4 | References (dense, numbered) |

### Minimal deck (1 slide)

Single-page memo. Hero + 3-bullet TL;DR + 4-row reference strip.

---

## Hero-slide patterns

Slide 5 is the visual centerpiece. The deck succeeds or fails on whether *this slide* communicates the structural thesis at a glance. Pattern names match the `hero_pattern` enum in [schema §9](../report-synthesis/references/schema-v0.1.md) — synthesis emits the enum value; renderer locates the recipe.

| `hero_pattern` | One-liner |
|---|---|
| `flow-process` | Left-to-right stages with arrows (default for architecture) |
| `architecture-layered` | Top-to-bottom tiers (system layers, abstraction levels) |
| `matrix-2x2` | Cartesian comparison (positioning, prioritisation, Eisenhower) |
| `timeline-events` | Chronological events (incident review, roadmap, history) |
| `hub-and-spoke` | Central concept with radiating branches (taxonomy, integration map) |
| `comparison-table` | Us vs them, before vs after (vendor selection, ROI) |
| `funnel-conversion` | Top-to-bottom narrowing (conversion, qualification, attrition) |
| `quadrant-bubble` | v0.2 — falls back to `matrix-2x2` |
| `evidence-grid` | v0.2 — falls back to `comparison-table` |

Each supported pattern has a ready HTML/CSS recipe in [references/hero-patterns.md](references/hero-patterns.md) plus guidance on when *not* to use it.

---

## Report-type variations

See [references/report-types.md](references/report-types.md) for full mappings. Quick reference:

| Report type | Hero pattern | Slides to emphasise | Slides to compress |
|-------------|--------------|---------------------|---------------------|
| Architecture brief | `flow-process` / `architecture-layered` | 5, 6, 7, 8, 9, 12 | 10 (often skip) |
| Research summary | `architecture-layered` / `matrix-2x2` | 3 (findings), 7, 10, 11 | 12, 13 |
| Postmortem | `timeline-events` | 3, 5, 6 (RCA), 14 (action items) | 12 |
| Strategy doc | `matrix-2x2` / `funnel-conversion` | 2 (TL;DR), 4 (thesis), 5, 13 | 10 |
| Technical RFC | `architecture-layered` / `comparison-table` | 4, 5, 7 (tradeoffs), 12 | 11 (use inline) |
| Investigation / audit | `timeline-events` | 3, 5, 6 (RCA), 11 (evidence), 14 | 12, 13 |
| Market / industry analysis | `matrix-2x2` / `comparison-table` | 3, 5, 7, 12 | 13 |
| Due-diligence memo | `comparison-table` / `matrix-2x2` | 3, 7, 10, 12 | 5, 13 |

---

## Visual storytelling

The deck has a distinct visual voice. See [references/visual-storytelling.md](references/visual-storytelling.md) for the principles in full. Six load-bearing moves:

1. **Atmospheric depth on cover** — radial bloom + vertical timecode column + accent ornament
2. **Pipeline with flow markers** — arrows, numbered stages, semantic colour progression
3. **Number-dominant stat tiles** — 54px figure (full) / 44px (brief), whisper label, corner accent stripe
4. **Card system with ambient glow** — tinted cards get a soft accent halo; hover lifts 1px with 18px shadow
5. **Staggered entrance reveal** — shead first, content cascades 120/200/280ms; CSS-only, disabled in print
6. **Semantic colour discipline** — five accents with fixed meanings; using amber for "info" is a bug

---

## Design tokens (immutable)

```css
:root{
  --bg:    #060d07;   /* page background */
  --sf:    #0c1a0e;   /* card / panel */
  --sf2:   #0f2113;   /* inset / code */
  --bd:    #1f3a25;   /* border subtle */
  --bd2:   #2a5333;   /* border emphasized */

  --tx:    #e8f5ea;   /* primary text */
  --tm:    #9fbfa6;   /* muted text */
  --td:    #6b8a72;   /* dim text */

  --gl:    #4ade80;   /* GREEN  · primary / stage / OK / approved */
  --aml:   #fbbf24;   /* AMBER  · anomaly / warning / soft alert */
  --cyl:   #22d3ee;   /* CYAN   · info / data / extraction */
  --pul:   #a78bfa;   /* PURPLE · synthesis / advanced / commercial */
  --rl:    #f87171;   /* RED    · reject / hard fail / critical */

  --bg-g:#0a1f10; --bg-a:#1a1408; --bg-c:#08141a; --bg-p:#120e1f; --bg-r:#1a0b0b;
}
```

**Accent semantics matter.** Readers calibrate to the system across slides. Using amber for "info" because amber looks nice is a bug — it breaks the reader's mental model.

### Light variant token map

All three `*-light.html` templates (full / brief / minimal) swap the token block below. Semantic meanings are unchanged — green still means OK, amber still means warning, etc. Only hue darkness/saturation shifts so the same accent reads cleanly on white.

```css
:root{
  --bg:#fafaf7;  --sf:#ffffff;  --sf2:#f1f4ef;   /* warm-tinted white, not pure #fff */
  --bd:#d9e2dc;  --bd2:#a8bdb0;

  --tx:#0c1a0e;  --tm:#4a5e4f;  --td:#6d7e72;    /* ink-green primary (墨綠) */

  --gl:#15803d;   /* GREEN  → forest      */
  --aml:#b45309;  /* AMBER  → burnt       */
  --cyl:#0e7490;  /* CYAN   → teal        */
  --pul:#6d28d9;  /* PURPLE → violet      */
  --rl:#b91c1c;   /* RED    → maroon      */

  --bg-g:#ecfdf5; --bg-a:#fef3c7; --bg-c:#ecfeff; --bg-p:#f3e8ff; --bg-r:#fee2e2;
}
```

Beyond the tokens, the light template also flips three visual conventions that don't survive a pure token swap:

| Detail | Dark | Light |
|---|---|---|
| Card elevation | `box-shadow: inset 0 1px 0 rgba(255,255,255,0.02)` (subtle top highlight) | `box-shadow: 0 1px 2px rgba(12,26,14,0.04)` (drop shadow under) |
| Cover/ornament glow | `box-shadow: 0 0 12px rgba(<accent>, 0.4-0.5)` (luminous bloom) | `box-shadow: 0 1px 4px rgba(<accent>, 0.25-0.35)` (depth without halo) |
| Nav chrome | `background: rgba(15,33,19,0.85)` (dark glass) | `background: rgba(255,255,255,0.92) + box-shadow` (white glass with lift) |

The card-tint border/shadow `rgba()` values are also rewritten to the new accent hex. Everything else (layout, typography, primitives, print contract, nav script, overflow contract) is identical between dark and light.

**`deck-minimal-light.html` has one extra delta**: `.metric` and `.minipipe .stage` add `box-shadow: 0 1px 2px rgba(12,26,14,0.05)`. The dark template leaves them flat because the surface tone already separates them from the background; on warm-white the same surfaces visually collapse into the page without a touch of elevation.

---

## Typography

- **Body**: `'IBM Plex Sans', 'Noto Sans TC', sans-serif` — Plex has stronger character at small sizes and a clinical voice. Never `Inter`, `Roboto`, or `system-ui` alone (generic AI feel).
- **Display (slide titles)**: Same family, 700 weight, tracked tight (`letter-spacing:-0.02em`).
- **Mono (formulas, code, IDs, eyebrows)**: `'IBM Plex Mono', monospace`. Pair with Plex Sans — same family, same designer's intent.
- **Numeric tabular**: `font-variant-numeric: tabular-nums` is set globally; tables and stat tiles inherit.

If the deck is Chinese-primary, lead with Noto Sans TC and use Plex as the Latin fallback in the font stack.

### Typography baselines (presentation-tuned, do not reduce)

Each template has its own baseline. You **MUST NOT** lower any size to "fit more text" — **cut text first.**

| Token | `deck-full.html` | `deck-brief.html` | `deck-minimal.html` |
|-------|-----------------:|------------------:|--------------------:|
| Body (p, ul, ol) | **18px** | **18px** | **18px** |
| h2 (slide heading) | **42px** | **40px** | n/a |
| h3 (block heading) | **22px** | **22px** | **22px** (mono variant) |
| Sub / proof line | **18px** | **17px** | **17px** |
| Table cells (td) | **18px** | **17.5px** | n/a |
| Table header (th, mono) | **14px** | **13.5px** | n/a |
| Info-box / `.ib` | **18px** | **17.5px** | **17px** (`.ask`) |
| Stat figure | **54px** | **44px** | **28px** (`.metric .mv`) |
| Stat label `.sl` | **13px** | **12px** | **12px** |
| Cover h1 | **78px** | **60px** | **44px** |
| Cover lede | **25px** | **20px** | **17px** |
| Eyebrow / scat / `.ct` | **13px** | **12.5px** | **13px** (`.eyebrow`) |
| Badge `.b` | **12.5px** | **12.5px** | n/a |
| Pipeline-stage desc | **16px** | **16px** | **16px** |
| Pipeline-stage title (mono) | **14px** | **13.5px** | n/a |
| Reference row title `.rt` | **17px** | **16px** | **14.5px** (`.refrow strong`) |
| Reference source `.rsrc` | **15.5px** | **14.5px** | **13px** |
| Reference link (mono) | **13.5px** | **13px** | **12.5px** |

Hard floor: body content **MUST NOT** go below 17px, mono labels **MUST NOT** go below 12.5px, references **MUST NOT** go below 13px. The previous baselines (body 16 / table 15 / labels 11) tested too small. If a slide can't fit at these sizes, **the slide has too much content** — cut, don't compress. The `.shead` headlines were already large; the bump targets the supporting text that readers actually need to scan.

### CJK / mixed-script width estimator (planning-time)

Use this **before** drafting a long title or wide row, to avoid a render-time overflow that forces a rewrite.

| Script | Effective width per character |
|---|---|
| CJK (漢字、ひらがな、한글) | ≈ font-size × **1.00** px |
| Latin / digits / Latin punctuation / spaces | ≈ font-size × **0.50** px |
| Mixed-script | sum per segment |

**Usable canvas widths** (1707 px canvas, 68 px horizontal padding on body slides → 1571 usable; cover uses 90 px padding → 1521 usable):

| Slot | Font size | Usable width | Max CJK chars | Max ASCII chars |
|---|---:|---:|---:|---:|
| `.shead h2` (full) | 42 px | 1571 px | ~37 | ~74 |
| `.shead h2` (brief) | 40 px | 1571 px | ~39 | ~78 |
| Cover `h1` (full) | 78 px | 1521 px | ~19 | ~38 |
| Cover `h1` (brief) | 60 px | 1521 px | ~25 | ~50 |
| Card `<h3>` in 2-col grid | 22 px | ≈ 740 px / col | ~33 | ~67 |
| Card `<h3>` in 3-col grid | 22 px | ≈ 490 px / col | ~22 | ~44 |
| Table cell body | 18 px | column-dependent | column-width ÷ 18 | column-width ÷ 9 |

**Apply when**:
- Drafting any title containing mixed Chinese + Latin (English technical terms embedded in a Chinese sentence).
- Adding rows to a table where one cell text looks long.
- Writing card `<h3>` titles in 3-col layout — width budget gets tight fast.
- Cover h1 + sub-line where both wrap to 2+ lines.

**Precedence with word caps** (see §Content concision rules): both the pixel-width estimator AND the word-cap apply — the **tighter** one wins. Pure-CJK content is usually pixel-bound (CJK chars are wide); ASCII-dominant content is usually word-bound (caps below ~8–10 words hit first). If your draft passes one but fails the other, rewrite to satisfy both.

**Limits of the estimator**: real glyph widths vary ±5 % by font; line-height, letter-spacing, and table padding are not included. Treat it as a **yes/no can-this-fit** tool, not as exact measurement. The canonical overflow script in §Overflow verification workflow remains the source of truth — it measures the actual rendered geometry.

---

## Content concision rules (mandatory)

Real-world testing showed verbose content shrinks effective font size and pushes the deck top-heavy. The **Hard cap** column below is a `MUST NOT exceed`; the **Soft target** is a `SHOULD`.

| Element | Hard cap | Soft target |
|---------|---------:|------------:|
| Slide headline (h2) | 24 words | 14 words |
| Sub-title (proof line) | 18 words | 12 words |
| Card heading (h3) | 6 words | 4 words |
| Card body sentence | 24 words | 16 words; max 2 sentences per card |
| Bullet (list item) | 14 words | 9 words |
| Stat label | 4 words | 2 words |
| Pipeline-stage caption | 8 words | 5 words |
| Reference description | 22 words | 14 words; one line |
| Table cell text | 10 words | 6 words; wrap rarely |

If a card needs more than 2 sentences, you **MUST** split it into two cards. If a bullet needs more than 14 words, you **MUST** demote it to its own card. You **MUST NOT** solve verbosity with smaller text.

---

## Layout primitives

| Class | Purpose | Notes |
|-------|---------|-------|
| `.slide` | The 1707×960 canvas | `padding: 46px 68px 62px`; `display:flex; flex-direction:column; justify-content:space-between` baseline |
| `.shead` | Slide heading block | `.scat` (eyebrow) · `h2` (headline) · `.sub` (proof) |
| `.g2 / .g3 / .g4` | Grid layouts | `gap: 24/18/14px` defaults |
| `.flex-col / .flex-row` | Flex stacks | `gap: 8/12px` defaults |
| `.card` | Bordered content block | + `.tint-g/-a/-c/-p/-r` for semantic variants |
| `.tw` | Table wrapper | Constrains width, bottom margin |
| `.ib` | Inline callout | + `.iba/.ibc/.ibp/.ibr` colour variants |
| `.b` | Inline badge | + `.bg/.ba/.bc/.bp/.br` colour variants |
| `.ref` | Reference row | Number bubble + title + source + link |
| `.stat` | Stat tile | + `.stat-a/-c/-p/-r` colour variants; supports `.unit` and `.delta` |
| `.pipeline / .pstage / .parrow` | Hero pipeline pattern | See hero-patterns.md |

### Vertical fill contract

`.slide { justify-content: space-between }` is the default. Cover slide overrides with `justify-content: center`. This pushes the closing/last block (footer, references, callouts) to the canvas bottom and forces content to breathe. You **MUST NOT** change the slide's `justify-content` per-slide except on cover. If a slide reads top-heavy, the fix is either more content, larger inner gaps via `.flex-col { flex: 1; justify-content: space-between }` on the main body block, or a per-slide `gap` bump — you **MUST NOT** use `justify-content: flex-start`.

---

## CSS specificity contract — READ THIS BEFORE EDITING

> **Inline `style="..."` on any element wins against ANY external selector**, including ID-scoped rules like `#s8 .card`. This is browser specificity, not a bug.

**Three ways to override an inline style:**

1. **Best (RECOMMENDED)**: Edit the HTML to remove or change the inline style.
2. **Acceptable (MAY)**: Use `!important` in the scoped CSS — but only inside `#sN ...` density-override blocks at the bottom of `<style>`. You **MUST NOT** use `!important` in base CSS.
3. **Never (MUST NOT)**: Reach for higher selector specificity. `body html #s8 div.card` does not beat `style="padding:10px"`.

Per-slide overrides live in a labelled block at the bottom of every template's `<style>`. Add overrides there, not scattered through the cascade.

### Inline-style policy (templates)

Inline styles are a pragmatic tool, not a sin — but typography/density inline silently breaks the baseline system and the floor checks in §6 of the verification checklist. Split by intent:

| Property | Inline allowed? | Why |
|---|:---:|---|
| `font-size`, `font-family`, `font-weight`, `line-height` | ✗ | Bypasses §Typography baselines and the hard floor (body 17 / mono 12.5 / refs 13). Use a class or per-slide override. |
| `padding` | ✗ | Belongs to the component (`.card`, `.ib`). Overriding inline drifts the density system. |
| `margin` | ✓ | Per-instance layout decision; moving every `margin-top:6px` into CSS just relocates noise without reducing it. |
| `color: var(--xx)` | ✓ | Semantic accent applied at the point of meaning. The token system is the abstraction; a class wrapper around it is over-engineering. |
| `gap`, `width`, `grid-template-columns`, `justify-content` | ✓ | One-off layout shape; the global classes handle the common case, inline handles the variant. |

If a banned property recurs across slides, promote it to a utility class (e.g. `.mono-block` for mono code containers) — not a per-slide override.

**Scope**: this policy applies to `templates/*.html`. Recipes in `references/hero-patterns.md` may use inline values for one-page readability — when copying a recipe into a template, factor the typography/density inline out of the snippet first.

---

## Print / PDF contract — UNIFIED SNIPPET

`deck-full.html` and `deck-brief.html` **MUST** include the print block below **verbatim**. `deck-minimal.html` uses a stripped 1-slide variant (no `#deck` reset, no `#nav/#bar` hide, no `:last-of-type` clause, `page-break-after: avoid`) because a single-slide deck cannot benefit from the multi-slide guards. The canonical block is the source of truth for multi-slide decks — `templates/deck-full*.html`, `templates/deck-brief*.html`, `references/pitfalls.md`, and `references/verification-checklist.md` **MUST** all match it exactly. Drift across the multi-slide templates is a documented bug class (see [pitfalls.md P-002 + P-003](references/pitfalls.md)).

```css
@page { size: A4 landscape; margin: 0; }
@media print {
  html, body { height: auto !important; overflow: visible !important; }
  #deck { position: static !important; height: auto !important; display: block !important; }
  .slide {
    position: relative !important;
    inset: auto !important;
    display: flex !important;
    flex-direction: column !important;
    justify-content: space-between !important;
    opacity: 1 !important;
    pointer-events: all !important;
    width: 100% !important;
    height: 100vh !important;
    overflow: hidden !important;
    page-break-after: always;
    break-after: page;
    page-break-inside: avoid;
    transition: none !important;
    animation: none !important;
  }
  #s1 { justify-content: center !important; }
  .slide:last-of-type { page-break-after: auto; break-after: auto; }
  .slide > *, .slide *::before, .slide *::after { animation: none !important; transition: none !important; }
  #nav, #bar { display: none !important; }
}
```

**Why each line exists:**
- Screen mode hides slides with `position:absolute + opacity:0` → print must reset both
- Screen `body { overflow:hidden }` suppresses extra pages → print must reset to visible
- `justify-content: space-between` is mirrored from screen so vertical fill is preserved in PDF
- `#nav, #bar` are screen-only chrome → hide
- `page-break-after:always` forces one slide per page
- Last slide drops the break to avoid a trailing blank page
- `animation: none` on `.slide` AND descendants freezes the entrance cascade for deterministic PDF

**Test procedure**: Chrome → `Ctrl+P` → Destination: Save as PDF → Layout: Landscape → Margins: None → Scale: Default → preview should show exactly N pages for N slides.

---

## Overflow verification workflow (Chrome DevTools MCP) — MANDATORY

Every content edit **MUST** trigger a re-measure of all affected slides. The fixed-canvas contract is meaningless without measurement.

### Five gotchas that break naive measurement

1. **`overflow:hidden` clips `scrollHeight`** — `slide.scrollHeight - slide.clientHeight` returns 0 even when content overflows. Cannot be used.
2. **Viewport ≠ canvas in chrome-devtools-mcp** — the MCP browser window is typically 1282×670 (chrome eats space), not the 1707×960 canvas the deck is designed for. `clientHeight` reflects the viewport, not the canvas. Measurement must **force the slide to the canvas dimensions** before sampling.
3. **`position:absolute / fixed` children are out of flow** — they appear in `getBoundingClientRect()` but contribute zero to vertical space. Counting them double-inflates the sum on cover slides with `::before/::after` blooms.
4. **`parseFloat("normal")` returns NaN** — `getComputedStyle().rowGap` returns the string `"normal"` for non-grid layouts without explicit `gap`. Guard with `Number.isFinite()`.
5. **Sum-of-children misleads when vertical fill is active** — with `.slide { justify-content: space-between }` + body siblings `flex: 1 1 auto`, the body container *stretches* to fill remaining space. A sum-of-children measure will then always report near-zero headroom regardless of natural content size, hiding real overflow within the stretched container. **Use the deepest-descendant check instead.**

### Canonical script

The script + per-move rationale lives in [references/overflow-script.md](references/overflow-script.md). Copy from there verbatim — if it ever appears in two places, that file wins. Expected return shape: `{ canvas, results, failures, all_ok }`.

### Pass criteria (in this order)

1. **`all_ok === true`** — `overshoot === 0` for every slide. Any positive overshoot is a `MUST`-fail that **blocks delivery**.
2. **No `NaN` values** anywhere in the result. A `NaN` means the script hit something it can't measure → you **MUST** fix the script, not the slide.
3. **`contentBottom` between ≈ 820 and ≈ 940 on body slides** (canvas − bottom padding ≈ 898). Below 820 → under-filled (top-heavy, violates §Vertical fill contract), which you **SHOULD** fix before shipping. At 940+ → tight; further additions will overflow.
4. **Cover slide (`#s1`) is exempt** — its `contentBottom` is naturally ~700 because the cover uses `justify-content: center`, not space-between.

### Compression order (least → most invasive)

When a slide overflows:

1. Reduce `.shead { margin-bottom }` (26 → 16)
2. Reduce `.card { padding }` (16 20 → 12 16)
3. Reduce table `td, th { padding; font-size; line-height }` — biggest lever on dense slides; `line-height: 1.45` is the highest-impact change on multi-line cells
4. Reduce inter-block `gap` in flex/grid containers
5. Reduce explicit `h3` margins
6. **Last resort**: reduce slide's own `padding-top` or `padding-bottom` via `#sN`

If overflow exceeds 80px, the slide is overpopulated — you **MUST** cut content rather than compress further. You **MUST NOT** compress below the published baselines (see §Typography baselines); doing so breaks projection readability.

### After-edit protocol (non-negotiable)

After ANY edit to a template — even a one-word change — you **MUST** re-open the file in chrome-devtools-mcp and run the script. You **MUST NOT** declare the edit complete until you have seen the `all_ok: true` line. Trusting that "this small change couldn't possibly overflow" is how regressions ship.

---

## Reference citation discipline

**Insufficient** (rejected):
```html
<a href="https://arxiv.org/abs/2204.08387">LayoutLMv3</a>
```

**Required**:
```html
<div class="rt">{{Full title}} ({{VENUE YEAR · Institution}})</div>
<div class="rsrc">{{one-line description of contribution + relevance}}</div>
<a class="rl2" href="{{url}}" target="_blank">{{stable-id-or-domain}}</a>
```

Every reference carries:
1. **Title** — full name (not a marketing tagline)
2. **Venue + Year + Institution** in parentheses — credibility anchor
3. **Substantive one-line description** — what it does, why it's cited here
4. **Stable identifier** — arXiv ID, DOI, ISBN, repo path, RFC number; never just a marketing URL

**Venue precision matters.** "arXiv 2022" is not a venue — it's a preprint server. ECCV / CVPR / ACM MM / NeurIPS / ICDAR / IEEE S&P / USENIX / ACM CCS / NDSS are venues. If unsure, use WebSearch or WebFetch to confirm before publishing.

Non-academic citation patterns are equally rigorous:
- **Standards**: `ISO/IEC 27001:2022 — Information Security Management (ISO · BSI · ISO/IEC standard)`
- **Vendor reports**: `State of DevOps 2024 (DORA · Google Cloud · dora.dev/research)`
- **Regulations**: `NIST SP 800-53 Rev 5 (NIST · 2020 · csrc.nist.gov)`
- **Open-source projects**: `Apache Kafka 3.7 (Apache Software Foundation · Apache 2.0 · kafka.apache.org)`

---

## Selection-matrix conventions

The selection table (slide 12 in full deck) follows a strict schema. Columns vary by report type but **always include a priority column**:

| Architecture | Strategy | Postmortem | Research |
|---|---|---|---|
| 層 · 工具 · 授權 · 機構 · 用途 · 優先級 | 維度 · 選項 · 成本 · 風險 · ROI · 優先級 | 領域 · 動作 · 擁有者 · 期限 · 風險 · 優先級 | 方法 · 樣本 · 工具 · 限制 · 信賴度 · 優先級 |

**Priority badge vocabulary (capped):**
- `MVP 必備` / `Must` — green badge (`bg`)
- `Phase 1` / `Should` — cyan badge (`bc`)
- `Phase 2` / `Could` — amber badge (`ba`)
- `視需求` / `Won't (this cycle)` — purple badge (`bp`)

Sort by priority first, then by category within each priority.

License/cost/risk columns **MUST** be specific values and **MUST NOT** be qualitative hedge words ("medium risk", "moderate cost", "open source"). Write the actual licence (`Apache 2.0`), the actual figure (`$120/mo`), the actual risk (`vendor lock-in: 90-day exit window`).

---

## Forbidden patterns

Every item below is a **MUST NOT**. These appeared in earlier drafts and were corrected — do not regenerate them:

- Inline padding/margin tweaks scattered through HTML — break specificity, impossible to audit. Use scoped overrides.
- Purple gradients on white backgrounds — generic AI aesthetic.
- `Inter`, `Roboto`, `system-ui` as the only font choice — generic.
- Decorative emoji in headings (🚀 ✨ 💡) — undermines technical credibility.
- Drop shadows on flat dark surfaces — looks like Bootstrap 2014. Use ambient glow on tinted cards instead.
- Explicit "excludes X" callouts — politically loaded, just omit the items.
- Citations that are just URLs without venue + year + institution.
- Headlines that are topics ("Architecture Overview") instead of conclusions ("X integrates two branches into the existing axis").
- Stat tiles where the label is the same size as the number — number must dominate.
- Reducing font sizes to "fit more text" — cut the text instead.
- Slides where content clusters in the top half with empty space at the bottom — violates §Vertical fill contract.
- Verbose multi-sentence card bodies — violates §Content concision rules.

---

## Build sequence

When asked to make a new deck:

1. **Clarify the report type and arc** with the user (use AskUserQuestion if vague). Refer to [references/report-types.md](references/report-types.md).
2. **Pick the hero pattern** for slide 5. Refer to [references/hero-patterns.md](references/hero-patterns.md).
3. **Copy the appropriate template** (`deck-full.html` / `deck-brief.html` / `deck-minimal.html`) to the output location.
4. **Fill content slide-by-slide**, headline-first (conclusion sentence) then proof. Enforce §Content concision rules as you write.
5. **Add references** with full venue metadata — verify via WebSearch/WebFetch if unsure.
6. **Open in Chrome via chrome-devtools-mcp**, navigate the file.
7. **Run the overflow verification script** — fix any negative slide; investigate any slide with headroom > 400px (under-filled).
8. **Print preview check**: Ctrl+P → confirm exactly N pages, landscape, no orphaned content.
9. **Screenshot dense slides** (hero, tables, references) and visually confirm readability AND vertical balance (no clustering at top).
10. Run through [references/verification-checklist.md](references/verification-checklist.md) before declaring done.

---

## Template state (verified canonical)

The templates below are the canonical baseline. Re-verify after every meaningful edit and update this table. Headroom values are from the canonical script above, at 1707×960.

| Template | Slides | Last verified | Layout strategy | Notes |
|----------|-------:|---------------|-----------------|-------|
| `deck-full.html` | 14 | 2026-05-21 (post body-font bump) | flex column, space-between, fixed h-100vh | `contentBottom` 687–898, `all_ok: true` |
| `deck-full-light.html` | 14 | 2026-05-21 (theme inversion) | identical to `deck-full.html` | Structural parity with dark verified; browser-render pending |
| `deck-brief.html` | 4 | 2026-05-21 (post body-font bump) | flex column, space-between, fixed h-100vh | `contentBottom` 676–916, `all_ok: true` |
| `deck-brief-light.html` | 4 | 2026-05-21 (theme inversion) | identical to `deck-brief.html` | Structural parity with dark verified; browser-render pending |
| `deck-minimal.html` | 1 | 2026-05-21 (post body-font bump) | CSS grid (auto 1fr auto), single canvas | `contentBottom` 912/960, `all_ok: true` |
| `deck-minimal-light.html` | 1 | 2026-05-21 (theme inversion) | identical to `deck-minimal.html` | Structural parity with dark verified; browser-render pending; `.metric` and `.minipipe .stage` add `0 1px 2px` drop-shadow (white-on-warm-white needs elevation) |

If a verification run produces a min headroom > 30px lower than expected, **investigate before shipping** — something changed that shouldn't have.

---

## Locked template features (per-template parity)

These features are visual-storytelling load-bearing. Edits that remove them silently regress the deck's voice. If a refactor seems to require removing one, you **MUST** stop and surface it to the user first.

Per-template parity — only features present in the template's column are locked there. You **MUST NOT** claim parity where there is none.

| Feature | full | brief | minimal | Identifying selectors |
|---------|:----:|:-----:|:-------:|-----------------------|
| Atmospheric cover bloom | ✓ | ✓ | — | `#s1::before`, `#s1::after` |
| Vertical timecode column | ✓ | — | — | `#s1 .tc` (rotated -90°) |
| Accent ornament line | ✓ | ✓ | — | `.ornament` (64×2px or 48×2px) |
| Pulsing eyebrow dot | ✓ | ✓ | — | `.eyebrow .dot` + `@keyframes pulse` |
| `.accent` span in h1 | ✓ | ✓ | — | `#s1 h1 .accent` (green emphasis) |
| Staggered entrance reveal | ✓ | ✓ | — | `@keyframes reveal-up`, `.slide.active > *` cascade, restart trick in nav script |
| Card depth (inset highlight + tint glow) | ✓ | ✓ | — | `.card { box-shadow: inset 0 1px 0 ... }`, `.card.tint-X:hover { box-shadow: ... 18px ... }` |
| Stat tile (number-dominant) | ✓ | ✓ | — | `.stat`, `.stat .sv` (54/44px), `.stat::before` (corner stripe) |
| Pipeline with arrows + branch lane | ✓ (s5) | ✓ (s2 mini) | — | `.pipeline / .pstage / .parrow / .plane` (or `.mpipe / .mpstage / .mparrow`) |
| Semantic colour discipline | ✓ | ✓ | ✓ | tokens `--gl / --aml / --cyl / --pul / --rl` mapped to OK / warn / info / synthesis / reject |
| Print contract block (unified snippet) | ✓ | ✓ | ✓ | `@page { size: A4 landscape; margin: 0; }` + `@media print { ... }` |
| Headline-IS-conclusion abstraction | ✓ | ✓ | ✓ | `.shead h2` reads as sentence; `.scat` uses placeholder `{{HERO · e.g. ...}}` |
| Vertical fill (`justify-content: space-between`) | ✓ | ✓ | grid auto-fills | Cover overrides with `justify-content: center` |
| Multi-slide keyboard navigation | ✓ | ✓ | — | nav script: Arrow / Space / Home / End / progress bar |

If you add a new template variant, it **MUST** include every locked feature applicable to its scope, and you **MUST** update this table.

---

## Stability self-check (run before declaring complete)

After any edit, all four checks below **MUST** pass simultaneously — they are the minimum bar:

```
□  Canonical overflow script returns all_ok: true with no NaN
□  No slide (except cover) has headroom > 400px — would indicate under-fill / top-heavy layout
□  Print preview (Ctrl+P, A4 landscape, no margins) shows exactly N pages for N slides
□  Screenshot of densest slide visually shows full content, no truncation, no clustering at top edge
```

If any check fails, you **MUST NOT** declare the work done. You **MUST** surface the failure to the user with the specific slide id and the measurement output. The skill is "done" only when these four pass simultaneously.

---

## Skill files

| File | Purpose |
|------|---------|
| `templates/deck-full.html` | 14-slide chassis with all primitives (dark, default) |
| `templates/deck-full-light.html` | 14-slide chassis, inverted theme (warm white + ink-green) |
| `templates/deck-brief.html` | 4-slide executive condensation (dark, default) |
| `templates/deck-brief-light.html` | 4-slide brief, inverted theme |
| `templates/deck-minimal.html` | 1-slide standalone memo (dark, default) |
| `templates/deck-minimal-light.html` | 1-slide memo, inverted theme |
| `references/visual-storytelling.md` | Six load-bearing visual moves + colour semantics |
| `references/hero-patterns.md` | Nine `hero_pattern` recipes (7 supported in v0.1, 2 v0.2 with documented fallback) |
| `references/report-types.md` | Report-type → slide-arc emphasis mappings |
| `references/verification-checklist.md` | 10-section pre-delivery checklist |
| `references/overflow-script.md` | Canonical overflow + typography-floor measurement scripts (single source of truth) |
| `references/pitfalls.md` | Documented bugs + root causes + fixes |
| `references/evals.md` | Behavioural regression spec (expected behaviours when editing this skill) |
