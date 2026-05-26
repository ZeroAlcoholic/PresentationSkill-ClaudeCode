# Narrative template: strategy consulting deck

**Use for**: strategy recommendations (internal or client-facing), market-entry analyses, organizational design proposals, transformation programs, executive decision decks. The unifying property is *recommendation up front, defended by structured logic* — the audience wants the answer first, then the proof.

---

## Canonical arc (full, 14 slides)

| # | Role | Required? | Notes |
|---|---|---|---|
| 1 | cover | ✓ | Title as one-sentence recommendation |
| 2 | tldr | ✓ | Pyramid Principle top: the recommendation + 3 supporting arguments |
| 3 | context | ✓ | Situation (SCQA's S) — relevant market/internal context |
| 4 | thesis | ✓ | Complication + recommendation restated; the SCQA core |
| 5 | hero | ✓ | Usually `matrix-2x2`, `quadrant-bubble`, or `comparison-table` |
| 6 | detail (Argument 1) | ✓ | First pillar of the recommendation |
| 7 | detail (Argument 2) | ✓ | Second pillar |
| 8 | detail (Argument 3) | optional | Third pillar (MECE coverage) |
| 9 | comparison (options considered) | ✓ | Show what was rejected and why — strengthens the recommendation |
| 10 | branch (sensitivity / scenarios) | optional | What changes the answer? |
| 11 | timeline (implementation) | ✓ | Phasing of the recommended action |
| 12 | risk | ✓ | Risks + mitigations; pre-empts board pushback |
| 13 | recommendation | ✓ | The ask — decision needed, owner, date |
| 14 | appendix | ✓ | Backup data, methodology, glossary |

**Brief (4 slides)**: cover → tldr → hero → recommendation.
**Minimal (1 slide)**: recommendation role with action_title as full sentence.

---

## Stock slides (almost always present)

- **2×2 matrix** as hero (positioning, options on two axes, vendor comparison)
- **Issue tree / MECE branching** when arguments decompose hierarchically
- **Waterfall / bridge** if quantifying financial impact (value created/destroyed by initiative)
- **Options-considered table** with explicit pros/cons per alternative
- **Implementation phasing** as horizontal timeline with milestones

---

## Headline grammar

Active voice, recommendation-leaning, specific numbers. The headline IS the conclusion.

| ✓ Good | ✗ Bad |
|---|---|
| "Enter Vietnam via JV with [Partner X] to reach $50M ARR by 2028" | "Vietnam market entry options" (topic, not insight) |
| "Three pillars support the JV path: market access, regulatory, capex efficiency" | "Why we recommend the JV" (no content) |
| "Direct entry rejected: 3-year regulatory delay + $80M capex" | "Other options were considered" (vague) |

**Anti-patterns specific to this genre**:
- Topic-titles ("Market Overview", "Methodology") — every title must say something
- Buzzword headlines ("Leverage synergies to transform paradigm")
- Recommendations without quantified upside/downside
- Missing the "why not the alternatives" slide — weakens defensibility

---

## Citation register

Default: `forensic` for internal data, `equity` for external market data. Mix per slide as appropriate. When using third-party research, name the firm + report + date.

---

## Color semantics

Default: `default` (red=reject, green=recommend, amber/`--aml`=tension/tradeoff). Renderer maps these semantic roles to actual tokens.

---

## Tone register

Confident but not arrogant. Declarative. The audience wants a recommendation they can act on, not hedged analysis. Caveats belong in the risk slide, not embedded in every headline.

---

## Quality gates extra for this genre

- Every recommendation slide MUST have an explicit `decision_to_drive` from frontmatter restated
- Options-considered slide MUST cover ≥2 rejected alternatives with reason
- Sum of pillar arguments (detail slides) MUST collectively defend the recommendation (MECE check)
- "Ghost-deck test" especially critical here — exec who only reads titles must reach the recommendation
