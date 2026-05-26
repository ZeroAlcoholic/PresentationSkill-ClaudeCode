# outline.md / evidence.md — Schema v0.1

The formal contract between `report-synthesis` (producer) and downstream render skills (consumers: `dark-deck-report` today; `plain-document-report` and `bright-pitch-deck` later).

**Design lineage** (so future authors can find the load-bearing decisions):
- File-based handoff via Markdown + YAML — `daymade/slides-creator` (narrative-brief + content), `ComposioHQ/content-research-writer` (outline + research + sources)
- Per-slide structured fields + action-title rubric — Promptiers AI Presentation Toolkit
- Anti-fabrication weight hierarchy — `slides-creator` Core Law
- Subagent QA + placeholder grep — Anthropic official `skills/pptx`
- Three-act narrative spine (SCR / SCQA) — Minto Pyramid Principle
- Assertion-Evidence per slide — Michael Alley

---

## 1. File layout

A synthesis project is a directory the user owns. Convention:

```
<report-name>/
├── outline.md       ← THE handoff artifact, schema below
├── evidence.md      ← citation database, schema below
└── sources/         ← raw source documents (PDFs, transcripts, notes) — optional
```

`report-synthesis` writes `outline.md` + `evidence.md`. Render skills read them.

---

## 2. `outline.md` — frontmatter spec

YAML frontmatter at top of file. Fields marked **REQUIRED** must be present; consumers MUST fail loudly on missing required fields with a clear migration message.

```yaml
---
# === Versioning ===
schema_version: 0.1                    # REQUIRED

# === Identity ===
report_type: investigation             # REQUIRED, enum (§3)
title: "Q3 supplier audit findings"    # REQUIRED, deck title (≤80 chars)
author: "Compliance Team"              # OPTIONAL
date: 2026-05-22                       # REQUIRED, ISO 8601
version: "1.0"                         # OPTIONAL, document version

# === Audience contract ===
audience: "Director of Compliance, board sub-committee"  # REQUIRED
decision_to_drive: "Approve corrective action plan within 30 days"  # REQUIRED

# === Delivery context ===
delivery:
  format: deck                         # REQUIRED, enum: deck (only value in v0.1)
  depth: full                          # REQUIRED, enum: full | brief | minimal
  theme: dark                          # REQUIRED, enum: dark | light
  duration_minutes: 20                 # OPTIONAL, null if read-only
  channel: projection                  # OPTIONAL, enum: projection | print | email | read

# === Governing thought (SCR triplet) ===
# Picked by user from 2-3 candidates surfaced by synthesis.
# Auto-committing the resolution is forbidden (see Core Law).
governing_thought:
  situation: "..."                     # REQUIRED, one sentence
  complication: "..."                  # REQUIRED, one sentence
  resolution: "..."                    # REQUIRED, one sentence — the answer
  candidates_considered:               # OPTIONAL, audit trail
    - "Alternative resolution A"
    - "Alternative resolution B"
  why_picked: "..."                    # OPTIONAL, rationale

# === Renderer routing ===
compatible_renderers:                  # REQUIRED, list of render-skill names
  - dark-deck-report
narrative_template: investigation      # REQUIRED, enum: must equal filename stem in references/narrative-templates/. v0.1 values: investigation | strategy. (audit, postmortem are report_type aliases of investigation.)

# === Citation register ===
citation_register: forensic            # REQUIRED, enum (§4)

# === Color semantics override (optional) ===
color_semantics: default               # OPTIONAL, enum: default | finance

# === Source linkage ===
evidence_file: ./evidence.md           # REQUIRED, relative path
sources_dir: ./sources                 # OPTIONAL
---
```

---

## 3. `report_type` enum

MVP (v0.1) supports synthesis for:

| Value | Native render skill | Status |
|---|---|---|
| `investigation` | dark-deck-report | **v0.1** |
| `audit` | dark-deck-report | **v0.1** (alias of investigation; arc identical) |
| `postmortem` | dark-deck-report | **v0.1** (alias of investigation) |
| `strategy` | dark-deck-report | **v0.1** |
| `rfc` | dark-deck-report | v0.2 planned |
| `research` | dark-deck-report | v0.2 planned |
| `market-analysis` | dark-deck-report | v0.2 planned |
| `pitch` | bright-pitch-deck (future) | not in scope |
| `brand` | bright-pitch-deck (future) | not in scope |
| `regulatory` | plain-document-report (future) | not in scope |
| `earnings` | (TBD; semantic-color collision) | not in scope |

Synthesis MUST reject `report_type` values outside its supported set with a helpful message naming the future skill that will handle them.

---

## 4. `citation_register` enum

| Value | Format convention | Example |
|---|---|---|
| `forensic` | `Document name (date, p.X)` with exhibit refs `[E1]` | `Supplier contract clause 4.2(b) (2024-03-01) [E1]` |
| `academic` | `Author et al., Venue YEAR · DOI` | `Smith et al., NeurIPS 2024 · 10.1234/abc` |
| `equity` | `Source (date)` with disclosure footnote | `Bloomberg (2026-04-15) — see disclosures` |
| `pitch` | press logos / brand attribution | `As featured in WSJ, TechCrunch` |
| `engineering` | RFC ID / repo SHA / ticket ID | `RFC-2026-014; commit a1b2c3d; JIRA-5821` |
| `regulatory` | ICH/CFR section number / submission ID | `21 CFR 312.32; IND 145678` |

---

## 5. `color_semantics` override

| Value | Red means | Green means |
|---|---|---|
| `default` | reject / risk / failure | approve / safe / pass |
| `finance` | loss / down | gain / up |

Renderers MUST respect this when present; default if absent.

---

## 6. Slide body — per-slide structure

Each slide is an H2 heading followed by a YAML fenced code block then optional markdown body. Outer wrapper uses 4 backticks so the inner 3-backtick block is literal:

`````markdown
## Slide 1 — Cover

```yaml
role: cover                 # REQUIRED, enum (§7)
action_title: "Q3 supplier audit identifies $4.2M exposure across 12 vendors"  # REQUIRED, see §8
hero_pattern: null          # OPTIONAL, enum (§9), only for role=hero
bullets: []                 # OPTIONAL, list of strings
evidence_refs: [E1, E2]     # OPTIONAL, list of evidence keys from evidence.md
data_callouts: []           # OPTIONAL, numeric highlights for the renderer
quality:
  title_score: 92           # REQUIRED, 0–100, from action-title rubric
  ghost_deck_pass: true     # REQUIRED, boolean — does title alone tell story?
  rewrite_attempts: 0       # REQUIRED, integer, max 2 (bounded retry)
  action_required: false    # OPTIONAL, set true if rubric failed after 2 rewrites
```

**Speaker notes**: Open with thesis-as-sentence. Audience already knows audit scope; don't re-introduce.
`````

In real `outline.md` files the inner block uses standard 3-backtick fences.

---

## 7. `role` enum

Maps to slide positions in the deck chassis. Renderer is free to reorder for `depth: brief|minimal`.

| Role | Purpose |
|---|---|
| `cover` | Title + thesis-as-sentence + audience + date |
| `tldr` | 3–5 bullets summarising the entire deck |
| `context` | Problem / situation / scope — quantified |
| `thesis` | One-sentence answer + why this approach |
| `hero` | Centerpiece visual (matrix / timeline / architecture / etc.) |
| `detail` | Deep-dive on a finding / mechanism / option |
| `branch` | Side-pipeline / alternative angle / counterpoint |
| `comparison` | Options × criteria matrix |
| `evidence` | Citation-heavy block tying claims to sources |
| `timeline` | Chronology slide (investigation / postmortem) |
| `risk` | Risks & mitigations |
| `recommendation` | Action items / decisions requested |
| `appendix` | Methodology / chain-of-custody / glossary |

---

## 8. `action_title` rubric (0–100)

Adapted from Promptiers + MBB action-title standards. Score per criterion, weighted equally (20 points each).

| Criterion | Pass condition |
|---|---|
| Complete sentence | Subject + verb + insight, not a topic label |
| So-what test | A busy executive would care; states implication or recommendation |
| Active voice | "Team X identified Y" not "Y was identified" |
| ≤15 words / ≤2 lines | Hard cap |
| Specific numbers | Includes a count, %, currency, or named entity where data exists |

**Pass threshold**: ≥85. Below → rewrite (bounded to `rewrite_attempts ≤ 2` per slide), then surface to user.

---

## 9. `hero_pattern` enum (v0.1)

Renderer chooses concrete layout from a fixed catalog. Synthesis picks the *pattern*; renderer owns the pixels. All values are kebab-case; renderer section names MUST match these strings exactly.

| Value | When to use | dark-deck v0.1 |
|---|---|---|
| `flow-process` | Sequential pipeline, claim adjudication, ML stages | ✓ |
| `architecture-layered` | System diagrams (C4-style), stacked tiers | ✓ |
| `matrix-2x2` | Discrete 2×2 strategy options, vendor comparison, Eisenhower | ✓ |
| `timeline-events` | Investigation chronology, project history, incident | ✓ |
| `hub-and-spoke` | Central concept with radiating branches, taxonomy, integration map | ✓ |
| `comparison-table` | Options × criteria scoring with rec column | ✓ |
| `funnel-conversion` | Pipeline / yield / qualification / attrition | ✓ |
| `quadrant-bubble` | Gartner-style continuous-axis market position | v0.2 (renderer falls back to `matrix-2x2`) |
| `evidence-grid` | Citation-heavy hero mapping sources to claims | v0.2 (renderer falls back to `comparison-table`) |

Renderers MUST declare which values they support in their `compatible_renderers` registration. Synthesis MAY choose a v0.2 value; if the renderer doesn't support it, the renderer falls back as noted and warns the user.

---

## 10. `evidence.md` schema

```markdown
---
schema_version: 0.1
---

# E1
source: "Internal audit report, 2025-09-15.pdf, p.12"
type: document                 # enum: document | interview | dataset | paper | press | repo | ticket
verifiable: true               # boolean — can a reviewer reproduce/find this?
quote: "..."                   # OPTIONAL, exact text being cited
context: "Section 3.2, finding F-04"
provenance: "From sources/audit-2025-09-15.pdf"  # OPTIONAL, file path

# E2
source: "Supplier contract clause 4.2(b), executed 2024-03-01"
type: document
verifiable: true
quote: "..."
```

Evidence keys are alphanumeric (`E1`, `E2a`, `INT-03`). `outline.md` slides reference them via `evidence_refs`. Render skills MAY render footnote/citation blocks from this file.

---

## 11. Anti-fabrication law (Core Law — non-negotiable)

Synthesis MUST respect this weight hierarchy when producing `outline.md` content:

```
user's own words  >  approved external material  >  AI synthesis of facts  >  AI invention
```

- Direct user-supplied text wins.
- Quotes from `sources/` (with evidence entry) outrank paraphrases.
- AI-synthesized claims MUST have an `evidence_refs` entry.
- AI invention (no source) is forbidden in production output. If unavoidable for narrative continuity, mark the bullet `[AI-INFERRED]` so the user/reviewer can audit.

**Two-gate workflow** (renderers MUST enforce):
1. **Emit-time** (synthesis writes `outline.md`): `[AI-INFERRED]` markers are PERMITTED and surfaced to the user for review.
2. **Pre-render** (renderer consumes `outline.md`): `[AI-INFERRED]` markers MUST be resolved — either replaced with a cited fact + `evidence_refs` entry, or removed. Renderers MUST grep `outline.md` for `\[AI-INFERRED\]` before rendering and refuse with the offending bullet locations if any remain. This is the same grep gate as the placeholder check in §12.

---

## 12. Validation contract for render skills

A render skill consuming `outline.md` MUST:

1. Parse and validate `schema_version`. If major version mismatch → fail loudly with migration message.
2. Check `<this-skill-name>` appears in `compatible_renderers`. If not → refuse to render.
3. Validate `report_type` is in its supported set. If not → refuse with a pointer to the correct render skill (if any).
4. Run placeholder grep on `outline.md` BEFORE rendering: `grep -iE "xxxx|lorem|ipsum|tbd|todo|fixme|\[ai-inferred\]"`. If hits → refuse to render and report offending file:line list to user. `[AI-INFERRED]` is intentionally included per §11 two-gate workflow.
5. Run ghost-deck test: extract action titles in order and verify the story reads coherently.

---

## 13. Versioning policy

- `0.x` — pre-1.0, breaking changes allowed at any minor bump.
- `1.0+` — semantic versioning: minor = additive fields only; major = breaking.
- Render skills MUST declare which schema major versions they support and fail loudly on mismatch.
- This document is the single source of truth. Skill SKILL.md files reference it; they do not duplicate it.
