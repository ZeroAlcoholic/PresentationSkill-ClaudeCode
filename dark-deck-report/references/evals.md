# Evals — behavioural regression spec

Each scenario pins one load-bearing `MUST` to an observable expected behaviour, in Anthropic's recommended Agent-Skill structure (`query` / `files` / `expected_behavior`).

> **Honest scope:** no built-in runner exists — this is a **regression spec / review checklist**, not live enforcement. Read it back when editing the skill, or use it as the rubric for scoring a real run. The two runnable checks it references (overflow + typography-floor probes) live in [overflow-script.md](overflow-script.md).

```json
[
  {
    "id": "ddr-placeholder-block",
    "rule": "Mode A validation #4 — MUST refuse render on placeholder hits",
    "query": "Render this outline into a brief deck.",
    "files": ["outline.md (contains a 'TODO: add Q3 figure' line)"],
    "expected_behavior": [
      "Runs placeholder grep before rendering",
      "Refuses to render and reports the offending file:line",
      "Does NOT emit an HTML deck with the TODO baked in"
    ]
  },
  {
    "id": "ddr-overflow-cut-not-shrink",
    "rule": "Concision + floor — MUST cut content, MUST NOT shrink below the baseline",
    "query": "This slide has nine dense bullets; make it fit.",
    "files": ["deck-brief.html"],
    "expected_behavior": [
      "Overflow script returns all_ok:true after the edit (no clipped content)",
      "Typography-floor probe returns pass:true (no surface pushed below 17/12.5/13)",
      "Reduces bullet COUNT or splits the slide rather than lowering font-size to fit"
    ]
  },
  {
    "id": "ddr-citation-venue-precise",
    "rule": "Principle 5 — a bare URL is NOT an acceptable citation",
    "query": "Add a reference to the LayoutLMv3 paper.",
    "files": ["deck-full.html"],
    "expected_behavior": [
      "Reference carries full title + venue + year + institution + stable id",
      "Does NOT render a citation that is only a hyperlink",
      "Verifies the venue (not 'arXiv') via web search if unsure"
    ]
  }
]
```
