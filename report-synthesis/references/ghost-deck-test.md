# Ghost-deck test

**Source**: adapted from `Gabberflast/academic-pptx-skill` and Promptiers toolkit. A single validation pass that catches the most common deck failure: incoherent narrative hidden behind beautiful slides.

---

## The test

Extract the `action_title` of every slide in order. Read them top to bottom, as a list, with no other content visible. The result MUST be a coherent argument that:

1. Opens with the recommendation/finding (per `governing_thought.resolution`)
2. Builds the supporting case in logical order
3. Closes with the decision/ask (per `decision_to_drive`)

If reading titles alone leaves a reviewer asking "wait, but why?", "what's the actual point?", or "I don't understand the leap from slide N to N+1", the test fails. Rewrite the title(s) that broke the chain.

---

## When to run

- After synthesis produces v1 of `outline.md`, before any render.
- After any title rewrite (per action-title rubric).
- As a render-skill pre-flight check (render skills MAY refuse to render if ghost-deck fails).

---

## Pass condition

The reviewer (human or LLM in a fresh context) reads only the action titles in order and can:
- State the governing thought without seeing the SCR triplet
- Identify the recommendation without seeing the Recommendation slide body
- Explain why the recommendation is supported, citing only title text

If yes → `ghost_deck_pass: true` for all slides in `outline.md`.
If no → identify the broken transitions, rewrite those titles, re-test.

---

## Automation in pipeline

For each slide, set `quality.ghost_deck_pass`:
- `true` if the slide's title is self-contained AND flows from the prior slide's title
- `false` if reading it alone leaves the reader needing the slide body to understand

A `false` value on any slide blocks rendering. Synthesis MUST rewrite to clear it (within `rewrite_attempts ≤ 2`), then surface to user if it still fails.

---

## Anti-patterns this test catches

- **"Methodology" slide titles that say nothing** — even if the body is fine, the title breaks the narrative chain.
- **Implicit transitions** — e.g., a slide whose title only makes sense if you already remember slide N-2.
- **Buried recommendations** — recommendation arrives in body text but title doesn't carry it.
- **Decorative section dividers** with titles like "Findings" or "Recommendations" — these are pure chrome; either give them a real action title or merge into the next content slide.

---

## Example (passes)

```
1. Q3 supplier audit identifies $4.2M exposure across 12 vendors
2. Three findings, two recommendations, one decision needed by 2026-06-15
3. 47 vendors, $182M spend, 90-day audit window — scope per board charter
4. Root cause: 2024 re-papering exercise missed renewals on 12 MSAs
5. Exposure concentrates in 4 of 12 failing vendors — 71% of $4.2M
...
13. Adopt 3-step corrective plan: re-paper (30d), escrow (60d), sunset (90d)
14. Methodology: 47 vendors sampled per AICPA AT-C 105
```

A reader can follow the argument with no slide bodies.

## Example (fails)

```
1. Q3 Supplier Audit
2. Key Findings
3. Background
4. Root Cause Analysis
...
13. Recommendations
14. Appendix
```

Topic titles. Tells you nothing. Reader has no idea what the deck argues.
