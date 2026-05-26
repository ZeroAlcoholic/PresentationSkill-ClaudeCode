---
name: report-synthesis
description: |
  Use this skill any time the user gives you a pile of source material —
  multiple documents, transcripts, notes, papers, datasets, meeting recordings,
  research files, a folder of things — and asks for a deck / presentation /
  slide outline / narrative / executive summary / brief derived from them.
  Trigger when input is "a folder / set of unstructured material" and output
  needs to be "a presentable narrative structure with citations". Produces
  outline.md + evidence.md that downstream render skills (dark-deck-report
  today; plain-document-report and bright-pitch-deck when available) consume.
  Does NOT render visuals — narrative synthesis only. Supports forensic-
  analytical genres: investigation, audit, postmortem, strategy consulting
  (MVP). RFC, research summary, market analysis planned. Pitch / brand /
  regulatory genres are out of scope — different skills will handle them.
license: Personal use; no warranty.
---

# Report Synthesis

Turn a pile of source material into a typed, citation-rigorous outline that downstream render skills consume. **One job: editorial judgment.** The narrative spine, the governing thought, the action titles, the citations. Visual rendering is somebody else's problem.

This skill is opinionated about *editorial discipline* (anti-fabrication, action titles, ghost-deck coherence) and *handoff contract* (typed schema, evidence-keyed citations, renderer compatibility). It is **agnostic** about subject matter within its supported genre set.

---

## Quick Reference

**Inputs**
- A directory of source material the user owns (PDFs, transcripts, notes, datasets)
- The user's intended `report_type` (or ambiguous — synthesis classifies and confirms)
- The user's `audience` and `decision_to_drive`
- Delivery context (deck depth, theme, duration)

**Outputs (in a user-named project directory)**
- `outline.md` — structured slide-by-slide narrative with frontmatter (per [references/schema-v0.1.md](references/schema-v0.1.md))
- `evidence.md` — keyed citation database
- `sources/` — original material (user-managed; this skill reads only)

**Hand-off**: render skills declared in `compatible_renderers` consume these files. This skill never renders anything visual.

---

## Core Law (non-negotiable)

```
user's own words  >  approved external material  >  AI synthesis of facts  >  AI invention
```

Full spec: [references/core-law.md](references/core-law.md). Summary: cite or mark `[AI-INFERRED]`; never fabricate numbers, citations, or quotes.

---

## The Three-Move Spine

Every synthesis run is exactly three moves. Resist the urge to add stages.

### Move 1 — Classify report_type

Read source material headers, user prompt, and any explicit user statement. Determine which `report_type` enum value applies (see [references/schema-v0.1.md §3](references/schema-v0.1.md)).

**Confirm with the user before proceeding.** Show your reasoning in one sentence and the chosen type. If ambiguous (e.g., could be investigation OR audit), ask once.

If `report_type` falls outside MVP support (pitch / brand / regulatory / earnings), STOP and tell the user which future skill will handle it. Do not attempt to force-fit.

### Move 2 — Propose governing thought candidates

Read the source material thoroughly. Propose **2–3 candidate `governing_thought.resolution` sentences** (each with its situation + complication framing). Surface them to the user with the source evidence backing each.

**Do not auto-commit.** The user picks. This is the single non-negotiable human-in-the-loop checkpoint — the research that informed this skill (Pyramid Principle, Promptiers, Anthropic skill conventions) all converge on the same point: governing thought is the one decision an AI must never make alone.

Record the chosen resolution, list the alternatives in `candidates_considered`, and capture `why_picked` for the audit trail.

### Move 3 — Generate outline using SCQA opening + AE per slide

Apply the genre's narrative template (`references/narrative-templates/<report_type>.md`) to lay out the slide arc.

- **SCQA opening**: Slides 1–4 of any deck encode Situation → Complication → Question → Answer. This is universal across genres.
- **Assertion-Evidence per slide**: every slide has a full-sentence `action_title` (the assertion) and `evidence_refs` populated from `evidence.md` (the evidence).
- **Stock slides** for the genre: include the ones the genre template marks as required.
- **Action title rubric**: score every title against [references/action-title-rubric.md](references/action-title-rubric.md). Rewrite if <85. Max 2 rewrite attempts per slide.
- **Ghost-deck test**: read all action titles in order. Must form a coherent argument. See [references/ghost-deck-test.md](references/ghost-deck-test.md).

---

## Per-Genre Narrative Templates

Genre descriptors live in [references/narrative-templates/](references/narrative-templates/). MVP (v0.1) ships:

- [investigation.md](references/narrative-templates/investigation.md) — also handles `audit` and `postmortem` (aliases; same arc)
- [strategy.md](references/narrative-templates/strategy.md)

Each descriptor declares: canonical arc, stock slides, headline grammar, citation register default, color semantics, tone register, genre-specific quality gates.

Adding a new genre = adding a new descriptor file. **Do not write genre logic in code** — it lives in data per Track 4 design recommendation.

---

## Quality Gates

Run all four before declaring `outline.md` ready for render. Each gate explicitly **BLOCKS** (render cannot proceed) or **WARNS** (user is shown, may proceed).

| Gate | What it checks | Action |
|---|---|---|
| **Core Law evidence** | Every numeric/named/dated claim has `evidence_refs` OR is marked `[AI-INFERRED]` | **WARN** at emit-time: list unbacked bullets to user for review |
| **Action-title rubric** | Every `action_title` scores ≥85; register is declarative-factual per [references/neutral-language.md](references/neutral-language.md) | Rewrite (≤2 attempts per [references/action-title-rubric.md](references/action-title-rubric.md)). After 2 failed attempts: set `quality.action_required: true` on that slide, **WARN** user with score breakdown, continue with remaining slides — do not block the run on one title |
| **Ghost-deck test** | Action titles alone form coherent argument | **WARN**: list breaking title pairs; rewrite suggestions surfaced; user decides |
| **Placeholder grep** | No `lorem`, `xxxx`, `TBD`, `TODO`, `FIXME`, `[AI-INFERRED]` remain in `outline.md` | **BLOCK** render (renderer enforces; see [references/schema-v0.1.md §11](references/schema-v0.1.md) two-gate workflow) |

Placeholder grep command (run at pre-render gate):

```bash
grep -iE "lorem|xxxx|tbd|todo|fixme|\[ai-inferred\]" outline.md evidence.md
```

If grep returns hits at pre-render, render is refused with offending file:line list. `[AI-INFERRED]` is permitted at emit-time (Core Law allows it for narrative continuity) but MUST be resolved before render.

---

## Handoff Contract

Schema is the single source of truth: [references/schema-v0.1.md](references/schema-v0.1.md).

Render skills MUST:
1. Check `schema_version` major matches their supported version
2. Verify their name appears in `compatible_renderers`
3. Verify `report_type` is in their supported set
4. Run placeholder grep on rendered output
5. Run ghost-deck test on render output

Synthesis output MUST be parseable to the schema. If you can't satisfy a required field, fail loudly with the missing field name — never silently emit a partial outline.

---

## Renderer Routing Table

| report_type | compatible_renderers (today) | Future additions |
|---|---|---|
| investigation, audit, postmortem | `dark-deck-report` | — |
| strategy | `dark-deck-report` | — |
| rfc | `dark-deck-report` (v0.2) | — |
| research | `dark-deck-report` (v0.2) | — |
| market-analysis | `dark-deck-report` (v0.2) | — |
| pitch, brand | (none) | `bright-pitch-deck` |
| regulatory, clinical | (none) | `plain-document-report` |
| earnings | (none — semantic-color collision) | TBD |

When listing `compatible_renderers`, include only skill names that the user has confirmed installed. If unsure, ask once. Do not attempt to enumerate the filesystem — the user-confirmed list is the contract.

---

## Project Directory Convention

Create or reuse a directory the user names:

```
<report-name>/
├── outline.md
├── evidence.md
└── sources/        ← user owns; this skill reads only
    ├── audit-report-2025-09-15.pdf
    ├── interviews/
    │   └── 2025-10-08-vp-proc.md
    └── ...
```

If the user doesn't supply a directory name, derive from `title` (kebab-case, ≤40 chars).

---

## When to Invoke

- "I have a folder of [documents/transcripts/notes/papers]; help me turn it into a deck."
- "Synthesize these sources into a presentation outline."
- "Build me the narrative for an investigation/audit/strategy report from this material."
- The user asks for slides AND has supplied source material rather than asking you to invent content.

## When NOT to Invoke

- The user already has an outline and just wants it rendered → invoke the render skill directly.
- The user wants content invented from a topic with no sources → this skill won't do that; ask for sources first.
- `report_type` is pitch / brand / regulatory / earnings → tell the user which future skill will handle it.
- The user wants a single-slide memo with content they'll type themselves → direct use of render skill with depth=minimal is faster.

---

## Anti-Patterns

| Don't | Why |
|---|---|
| Auto-commit a governing thought without surfacing candidates | Violates the one irreducible human checkpoint |
| Fabricate numbers, dates, citations, or quotes | Core Law violation; destroys credibility |
| Emit `outline.md` with bullets lacking `evidence_refs` | Render skills will reject; cite-or-mark-INFERRED |
| Write genre logic in code instead of descriptor files | Adds friction for new genres; breaks the "data not code" pattern |
| Stack methodologies (Pyramid + Sparkline + Raskin + Roam) | Track 2 research: produces incoherent decks; pick one spine (SCQA + AE) |
| Build an 8-stage pipeline like ppt-creator | Track 3: three moves cover ≥90% of value; later stages add latency, not quality |
| Compute charts or numbers in head | Ask user for source; mark `[USER-PLEASE-VERIFY]` if necessary |
| Skip the ghost-deck test because "the slides look fine" | The test catches narrative incoherence that hides behind good visuals |
| Force-fit out-of-scope genres into MVP support | Pitch decks need bright-pitch-deck; regulatory needs A4 portrait |

---

## Dependencies

- Read/Write for source material and output files
- Bash for `grep` (placeholder gate) and `ls` (directory listing)
- No Python required for v0.1; LLM applies rubrics inline. Scripts may be added in v0.2 for determinism.

---

## Design Lineage

Load-bearing decisions trace to:
- **File-based handoff (outline + evidence)** — `daymade/slides-creator` (narrative-brief + content), `ComposioHQ/content-research-writer` (outline + research)
- **Per-slide structured fields + rubric** — Promptiers AI Presentation Toolkit
- **Anti-fabrication Core Law** — `daymade/slides-creator`
- **SKILL.md canonical structure** — `anthropic/skills/skills/pptx`
- **Ghost-deck test** — `Gabberflast/academic-pptx-skill`
- **Three-move spine over multi-stage pipelines** — research synthesis across four tracks (see project conversation log)

For the full schema spec including all field definitions, enums, and validation contracts, see [references/schema-v0.1.md](references/schema-v0.1.md). All other reference files derive from or operationalize that spec.
