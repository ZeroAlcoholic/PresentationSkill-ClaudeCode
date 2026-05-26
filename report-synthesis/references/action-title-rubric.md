# Action-title rubric (0–100)

Adapted from Promptiers AI Presentation Toolkit + MBB consulting standards. Applied per slide to the `action_title` field.

---

## Scoring (20 points each, total 100)

### 1. Complete sentence (20)
Subject + verb + object/insight. Not a topic label.

| Score | Example |
|---|---|
| 20 | "Q3 audit identified $4.2M exposure across 12 vendors" |
| 10 | "Q3 audit findings on supplier exposure" |
| 0 | "Audit Overview" |

### 2. So-what test (20)
A busy executive would care. States an implication, recommendation, or load-bearing fact — not a description of the slide's contents.

| Score | Example |
|---|---|
| 20 | "26% of vendors failed contractual controls — escalate before Q4 close" |
| 10 | "Several vendors had control issues" |
| 0 | "Vendor control test results" |

### 3. Active voice (20)
"Team X did Y" not "Y was done by Team X". Subject acts on the verb.

| Score | Example |
|---|---|
| 20 | "Procurement re-papered 12 vendors in Q2" |
| 10 | "Vendors were re-papered in Q2" |
| 0 | "Re-papering exercise (Q2)" |

### 4. ≤15 words / ≤2 lines (20)
Hard cap. Renderers truncate or wrap awkwardly past this.

| Score | Condition |
|---|---|
| 20 | ≤15 words |
| 10 | 16–20 words |
| 0 | >20 words |

### 5. Specific numbers (20)
A count, %, currency, named entity, date, or other concrete anchor. Where data exists, use it.

| Score | Example |
|---|---|
| 20 | "12 vendors expose $4.2M of disputed billing as of 2025-10-15" |
| 10 | "Some vendors expose material disputed billing" |
| 0 | "Vendors create exposure" |

**Exemption**: if no quantifiable claim exists for the slide content (e.g., a methodology slide), score full points if the title is still specific and declarative.

---

## Pass threshold

**≥85** total. Below threshold → rewrite (max `rewrite_attempts: 2`), then surface to user for manual decision.

---

## Anti-patterns (auto-fail to score=0 on relevant criterion)

- **Topic labels** ("Methodology", "Background") — fails Complete sentence
- **Buzzwords**: leverage, synergy, paradigm, harness, transform, robust, holistic, seamless — flag and require rewrite
- **Em-dashes used decoratively** (not connecting clauses) — style violation, surface but don't fail
- **Generic ranges without anchor**: "Some / Many / Multiple / Various" — fails Specific numbers
- **Persuasive-prescriptive register** (zh: 必須/不能/面臨壓力/我們建議; en: must/should/critical/transformative) — caps criterion #2 (so-what) at ≤10; rewrite to declarative-factual per [[neutral-language]]. MVP genres are forensic-analytical; the register must read as fact, not advocacy.

---

## Application in pipeline

1. Synthesis drafts `action_title` for each slide.
2. Score against rubric (LLM applies criteria; future: `scripts/score-titles.py`).
3. If score <85, rewrite once.
4. If still <85, rewrite a second time.
5. If still <85, surface to user with the score breakdown and ask for manual edit or override.
6. Write final `title_score` and `rewrite_attempts` to slide `quality` block.
