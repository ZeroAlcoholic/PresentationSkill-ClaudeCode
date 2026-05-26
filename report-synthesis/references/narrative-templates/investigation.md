# Narrative template: investigation / audit / postmortem

**Use for**: forensic investigations, internal audits, security postmortems, incident reviews, due-diligence memos. The unifying property is *facts before recommendations* — the audience must believe the findings before they'll accept the action items.

---

## Canonical arc (full, 14 slides)

| # | Role | Required? | Notes |
|---|---|---|---|
| 1 | cover | ✓ | Title as one-sentence finding-summary |
| 2 | tldr | ✓ | 3–5 bullets: top findings, top recommendations, decision sought |
| 3 | context | ✓ | Scope, charter, timeframe, methodology (1-line each) |
| 4 | thesis | ✓ | One-sentence answer; restate the asks |
| 5 | hero | ✓ | Usually `timeline-events` or `matrix-2x2` (exposure × severity) |
| 6 | detail (Finding 1) | ✓ | First major finding with evidence_refs |
| 7 | detail (Finding 2) | ✓ | Second major finding |
| 8 | branch (Finding 3 or root-cause analysis) | optional | |
| 9 | comparison (options considered) | optional | If multiple corrective paths exist |
| 10 | evidence | optional | Citation-heavy block — chain of custody summary |
| 11 | timeline | ✓ | Chronology of events; pre-empts "when did X happen?" |
| 12 | risk | optional | What could still go wrong post-recommendation |
| 13 | recommendation | ✓ | Action items with owners and dates |
| 14 | appendix | ✓ | Methodology, sampling, chain-of-custody hash list |

**Brief (4 slides)**: cover → tldr → hero → recommendation.
**Minimal (1 slide)**: tldr role with action_title carrying the recommendation.

---

## Stock slides (almost always present)

- **Chain-of-custody summary** in appendix — every exhibit's SHA-256 hash + custodian log
- **Timeline with exhibit anchors** — events on a horizontal axis, exhibit IDs as callouts
- **Facts vs inferences callout** — visually distinct so a reviewer can audit AI/analyst inferences
- **Scope statement** as part of context slide — pre-empts "did you cover X?"

---

## Headline grammar

Past-tense, factual-neutral, citation-anchored. Never speculative.

| ✓ Good | ✗ Bad |
|---|---|
| "Q3 audit identified $4.2M exposure across 12 vendors" | "Audit reveals serious problems" (no specific) |
| "Re-papering exercise (2024) missed renewals on 12 master agreements" | "Past process failures contributed to exposure" (passive, vague) |
| "Escrow mechanism is permitted under all 12 MSAs (§4.2(b))" | "We can use escrow" (informal, no source) |

**Anti-patterns specific to this genre**:
- Speculative headlines without `[AI-INFERRED]` marker
- Headlines that lead with the recommendation before establishing facts (audience won't trust the ask)
- Hyperbole ("massive", "serious", "critical") without quantification

---

## Citation register

Default: `forensic`. Every claim referenceable to a specific document, page, exhibit, or interview. Quotes preserved verbatim where load-bearing.

---

## Color semantics

Default: `default` (red=reject/risk, green=approve/safe, amber/`--aml`=attention). Renderer maps these semantic roles to actual tokens.

---

## Tone register

Conservative, neutral, restrained. Never editorialise. The audience is reviewing your work — they will discount findings if the tone reads as advocacy.

---

## Quality gates extra for this genre

- Every claim with a number MUST have `evidence_refs` populated
- `chain_of_custody` field in appendix slide is non-optional (will be added v0.2)
- AI-inferred bullets MUST be marked `[AI-INFERRED]` at emit-time and resolved (replaced with cited fact, or removed) before render — see [[../core-law]] for the two-gate workflow
