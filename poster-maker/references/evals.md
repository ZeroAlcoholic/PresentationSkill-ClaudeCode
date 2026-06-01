# Evals — behavioural regression spec

Each scenario pins one load-bearing `MUST` to an observable expected behaviour, in Anthropic's recommended Agent-Skill structure (`query` / `files` / `expected_behavior`).

> **Honest scope:** no built-in runner exists — this is a **regression spec / review checklist**, not live enforcement. Read it back when editing the skill, or use it as the rubric for scoring a real run. The three runnable checks it references live in [verification.md](verification.md).

```json
[
  {
    "id": "pm-overflow-trim-not-shrink",
    "rule": "Rule 2 + 3 — content MUST fit; MUST NOT drop below the body floor",
    "query": "Make a poster from this 1500-word report.",
    "files": ["report.md"],
    "expected_behavior": [
      "Applies P0/P1/P2 triage: cuts P2 paragraphs / extra sources rather than shrinking type",
      "Overflow probe returns all_ok:true; typography-floor probe returns pass:true (≥26px portrait)",
      "Does NOT reduce --fs-body below the calibrated default to fit more text"
    ]
  },
  {
    "id": "pm-zone-presence",
    "rule": "Zone presence — MUST remove a zone it can't fill",
    "query": "Make a poster from these three qualitative themes (no numbers).",
    "files": ["themes.md"],
    "expected_behavior": [
      "Removes the stats strip and hero-stat-block (no dominant numeric stat exists)",
      "Does NOT fabricate numbers to fill the stat zones (Rule 1)",
      "Delivers a poster with no empty or half-filled zones"
    ]
  },
  {
    "id": "pm-no-placeholder-tokens",
    "rule": "Output checklist + verification.md #1 — MUST ship zero tokens",
    "query": "Generate the poster HTML.",
    "files": ["poster-infographic-portrait.html"],
    "expected_behavior": [
      "Placeholder scan finds no {{ tokens and no LABEL_* stubs in any inline SVG",
      "Every optional <!-- --> section with no content is removed, not left empty",
      "Does NOT deliver an HTML file with an unfilled token"
    ]
  }
]
```
