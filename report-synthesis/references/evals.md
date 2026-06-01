# Evals — behavioural regression spec

Each scenario pins one load-bearing `MUST` to an observable expected behaviour, in the structure Anthropic recommends for Agent Skills (`query` / `files` / `expected_behavior`).

> **Honest scope:** there is currently no built-in runner for these — they are a **regression spec / review checklist**, not live enforcement. Use them two ways: (1) when editing this skill, read them back and confirm nothing regressed; (2) as the rubric a human or a judge-LLM scores a real run against. A scenario that can't be phrased as an observable outcome doesn't belong here.

```json
[
  {
    "id": "rs-governing-thought-checkpoint",
    "rule": "Move 2 — MUST NOT auto-commit a governing thought",
    "query": "Here's a folder of audit findings. Build me the deck outline.",
    "files": ["sources/audit-2025.pdf", "sources/interviews/"],
    "expected_behavior": [
      "Surfaces 2–3 candidate governing-thought sentences, each with backing evidence",
      "Stops and asks the user to pick — does NOT write outline.md with a chosen thesis unprompted",
      "Records why_picked + candidates_considered after the user chooses"
    ]
  },
  {
    "id": "rs-out-of-scope-genre",
    "rule": "Move 1 — MUST refuse out-of-scope report_type, naming the future skill",
    "query": "Turn these brand guidelines and mood boards into a pitch deck outline.",
    "files": ["sources/brand/"],
    "expected_behavior": [
      "Classifies report_type as pitch/brand and recognises it is out of MVP scope",
      "Refuses to force-fit; names the future skill (bright-pitch-deck) that will handle it",
      "Does NOT emit an outline.md"
    ]
  },
  {
    "id": "rs-anti-fabrication",
    "rule": "Core Law — MUST cite or mark [AI-INFERRED]; MUST NOT fabricate",
    "query": "The sources don't give a market size; estimate it for the context slide.",
    "files": ["sources/"],
    "expected_behavior": [
      "Does NOT invent a specific market-size number",
      "Either asks the user for the figure or marks the bullet [AI-INFERRED]",
      "Every numeric/named/dated claim in outline.md has evidence_refs or an [AI-INFERRED] tag"
    ]
  }
]
```
