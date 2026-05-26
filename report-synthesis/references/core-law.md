# Core Law: anti-fabrication weight hierarchy

**Source**: adapted from `daymade/slides-creator` Core Law. The single most important guardrail in this skill — every other quality gate exists to enforce variations of this principle.

---

## The hierarchy (strictly ordered)

```
user's own words  >  approved external material  >  AI synthesis of facts  >  AI invention
```

When drafting any field of `outline.md` or `evidence.md`, prefer content drawn from sources higher in the hierarchy.

| Tier | Source | Allowed in output? | Marking needed? |
|---|---|---|---|
| 1 | Direct quote from user-provided text (interview, message, doc) | YES | Quote it verbatim |
| 2 | Quote/paraphrase from a document in `sources/` with evidence entry | YES | `evidence_refs` mandatory |
| 3 | AI synthesis combining tier-1/2 facts | YES, conditionally | `evidence_refs` mandatory |
| 4 | AI invention (no source) | **No, except as marked** | `[AI-INFERRED]` prefix mandatory |

---

## Operational rules

**Rule 1 — Direct quote precedence**.
If the user said it, use their words. Don't rephrase user-supplied quotes into AI-flavored summaries.

**Rule 2 — Evidence requirement**.
Every claim containing a number, named entity, date, or factual assertion in `outline.md` MUST cite an `evidence_refs` entry. No exceptions.

**Rule 3 — Synthesis traceability**.
When synthesising across multiple sources, list all source evidence keys in `evidence_refs`. A reader must be able to retrace the reasoning.

**Rule 4 — Invention marking**.
If narrative continuity demands a bullet for which there is no source (e.g., a transition sentence, an obvious implication), prefix the bullet with `[AI-INFERRED]`. Don't hide it. The reviewer decides whether to keep, rewrite, or remove.

**Rule 5 — Don't compute numbers**.
Numeric claims (totals, percentages, deltas, forecasts) MUST come from a `dataset`-type evidence entry. Do not arithmetic in your head; ask the user to provide the computed number with its source, or include a `[USER-PLEASE-VERIFY]` marker.

**Rule 6 — Don't invent citations**.
Never fabricate a paper title, author, DOI, court case, regulation number, or document reference. If a citation is needed and not in `sources/`, ask the user.

---

## Why this matters

The four research tracks that informed this skill all surface the same failure mode: **AI-generated decks lose credibility faster from one wrong number than from any aesthetic flaw**. Commercial tools (Gamma, Tome) and Claude-skill prior art (ppt-creator, academic-pptx-skill) all explicitly warn against this. The cost of restraint is occasional friction asking the user for sources. The cost of fabrication is the entire deliverable being discarded — and trust permanently lost.

---

## Enforcement: two-gate workflow

Per [[schema-v0.1]] §11, `[AI-INFERRED]` is governed by two distinct gates with different rules:

| Gate | When | Rule | Failure action |
|---|---|---|---|
| **Emit-time** | Synthesis writes `outline.md` | `[AI-INFERRED]` PERMITTED for narrative continuity | **WARN**: list all marked bullets to user for review |
| **Pre-render** | Renderer consumes `outline.md` | `[AI-INFERRED]` MUST be resolved (cited fact + `evidence_refs`, OR removed) | **BLOCK**: renderer refuses with file:line list |

Same gates apply to bullets lacking `evidence_refs`:
- **Emit-time**: WARN user with count of unbacked bullets.
- **Pre-render**: BLOCK if the bullet contains numeric/named/dated claims (Rule 2).

Synthesis is expected to drive `[AI-INFERRED]` count to zero before invoking the renderer.
