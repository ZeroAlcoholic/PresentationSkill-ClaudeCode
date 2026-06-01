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

## Conformance language

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL** in this document are to be interpreted as described in RFC 2119 and RFC 8174 — **and only when they appear in ALL CAPITALS.** A lowercase "must" / "should" / "may" is ordinary prose, not a normative requirement.

Each level maps to a concrete runtime consequence in this skill ecosystem:

| Level | Meaning | Consequence if unmet |
|---|---|---|
| **MUST** / **MUST NOT** / **REQUIRED** | Absolute requirement / prohibition | **BLOCK** — stop; do not emit or hand off |
| **SHOULD** / **SHOULD NOT** / **RECOMMENDED** | Strong default; deviating REQUIRES a stated reason | **WARN** — surface the deviation to the user, then MAY proceed |
| **MAY** / **OPTIONAL** | Genuine free choice | none |

**Assigning BLOCK vs WARN (Design-by-Contract lens).** When deciding a new rule's level, attribute the fault: a **precondition** failure (missing or invalid *input* the skill depends on) is the caller's fault — refuse or ask the user rather than guessing. A **postcondition** failure (the skill's *own output* violates a rule) is the skill's fault — BLOCK and fix before delivery; never ship it behind a warning. An **invariant** (must hold throughout, e.g. output stays parseable to the schema) is always a `MUST`.

This vocabulary is shared verbatim by the sibling render skills (`dark-deck-report`, `poster-maker`), so a `MUST` in any one of them carries the same weight here.

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

## Core Law

```
user's own words  >  approved external material  >  AI synthesis of facts  >  AI invention
```

Full spec: [references/core-law.md](references/core-law.md). In normative terms: synthesis **MUST** cite every numeric, named, dated, or quoted claim, or mark it `[AI-INFERRED]`. It **MUST NOT** fabricate numbers, citations, or quotes. `[AI-INFERRED]` **MAY** remain at emit-time for narrative continuity, but it **MUST** be resolved before any renderer consumes the file (enforced as **BLOCK** at the pre-render placeholder gate).

---

## The Three-Move Spine

Every synthesis run is exactly three moves. You **MUST NOT** add stages — three moves cover ≥90% of value; later stages add latency, not quality.

### Move 1 — Classify report_type

Read source material headers, user prompt, and any explicit user statement. Determine which `report_type` enum value applies (see [references/schema-v0.1.md §3](references/schema-v0.1.md)).

You **MUST** confirm the type with the user before proceeding: show your reasoning in one sentence and the chosen type. If ambiguous (e.g., could be investigation OR audit), you **MUST** ask once rather than guess.

If `report_type` falls outside MVP support (pitch / brand / regulatory / earnings), you **MUST** stop and name the future skill that will handle it. You **MUST NOT** force-fit an out-of-scope genre.

### Move 2 — Propose governing thought candidates

Read the source material thoroughly. Propose **2–3 candidate `governing_thought.resolution` sentences** (each with its situation + complication framing). Surface them to the user with the source evidence backing each.

You **MUST NOT** auto-commit a governing thought — the user picks. This is the single irreducible human-in-the-loop checkpoint: the research that informed this skill (Pyramid Principle, Promptiers, Anthropic skill conventions) all converge on the point that governing thought is the one decision an AI **MUST NOT** make alone.

You **MUST** record the chosen resolution, list the alternatives in `candidates_considered`, and capture `why_picked` for the audit trail.

### Move 3 — Generate outline using SCQA opening + AE per slide

Apply the genre's narrative template (`references/narrative-templates/<report_type>.md`) to lay out the slide arc.

- **SCQA opening**: Slides 1–4 of any deck **MUST** encode Situation → Complication → Question → Answer. This is universal across genres.
- **Assertion-Evidence per slide**: every slide **MUST** have a full-sentence `action_title` (the assertion); every slide **SHOULD** carry `evidence_refs` from `evidence.md` (the evidence), and any claim without them **MUST** be marked `[AI-INFERRED]` per the Core Law.
- **Stock slides**: you **MUST** include the slides the genre template marks as required.
- **Action-title rubric**: score every title against [references/action-title-rubric.md](references/action-title-rubric.md). A title scoring <85 **MUST** be rewritten (≤2 attempts per slide; see the Quality Gates table for what happens after 2 failed attempts).
- **Ghost-deck test**: read all action titles in order — they **MUST** form a coherent argument. See [references/ghost-deck-test.md](references/ghost-deck-test.md).

---

## Per-Genre Narrative Templates

Genre descriptors live in [references/narrative-templates/](references/narrative-templates/). MVP (v0.1) ships:

- [investigation.md](references/narrative-templates/investigation.md) — also handles `audit` and `postmortem` (aliases; same arc)
- [strategy.md](references/narrative-templates/strategy.md)

Each descriptor declares: canonical arc, stock slides, headline grammar, citation register default, color semantics, tone register, genre-specific quality gates.

Adding a new genre = adding a new descriptor file. Genre logic **MUST** live in descriptor data, not in code (Track 4 design recommendation) — this keeps new genres frictionless.

---

## Quality Gates

You **MUST** run all four gates before declaring `outline.md` ready for render. Each gate is either **BLOCK** (a `MUST` — render cannot proceed) or **WARN** (a `SHOULD` — the user is shown the deviation and MAY proceed).

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

If grep returns hits at pre-render, render **MUST** be refused with the offending file:line list. `[AI-INFERRED]` is permitted at emit-time (Core Law allows it for narrative continuity) but **MUST** be resolved before render.

---

## Handoff Contract

Schema is the single source of truth: [references/schema-v0.1.md](references/schema-v0.1.md).

Render skills MUST:
1. Check `schema_version` major matches their supported version
2. Verify their name appears in `compatible_renderers`
3. Verify `report_type` is in their supported set
4. Run placeholder grep on rendered output
5. Run ghost-deck test on render output

Synthesis output **MUST** be parseable to the schema. If you cannot satisfy a `REQUIRED` field, you **MUST** fail loudly with the missing field name; you **MUST NOT** silently emit a partial outline.

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

When listing `compatible_renderers`, you **MUST** include only skill names the user has confirmed installed; if unsure, you **MUST** ask once. You **MUST NOT** enumerate the filesystem to guess — the user-confirmed list is the contract.

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

Every row below is a **MUST NOT** — doing any of these is a defect, not a style choice.

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

When editing this skill, read back [references/evals.md](references/evals.md) — a behavioural regression spec that pins the load-bearing `MUST`s (governing-thought checkpoint, out-of-scope refusal, anti-fabrication) to observable expected behaviours.

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
