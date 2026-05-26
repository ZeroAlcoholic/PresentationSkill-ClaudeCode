---
schema_version: 0.1

# === Identity ===
report_type: investigation
title: "Q3 supplier audit — exposure and corrective action"
author: "Compliance Team"
date: 2026-05-22
version: "1.0"

# === Audience contract ===
audience: "Director of Compliance, board sub-committee (3 readers, 20-minute slot)"
decision_to_drive: "Approve the corrective action plan in §Recommendation by 2026-06-15"

# === Delivery context ===
delivery:
  format: deck
  depth: full
  theme: dark
  duration_minutes: 20
  channel: projection

# === Governing thought (SCR triplet) ===
governing_thought:
  situation: "Q3 supplier audit covered 47 active vendors representing $182M annual spend."
  complication: "12 vendors (26%) failed contractual control checks, exposing $4.2M to disputed billing."
  resolution: "Adopt the three-step corrective plan (re-paper, escrow, sunset) to close exposure within 90 days."
  candidates_considered:
    - "Adopt corrective plan (chosen)"
    - "Terminate the 12 failing vendors immediately (rejected: supply continuity risk)"
    - "Defer action pending Q4 audit (rejected: $4.2M continues to compound)"
  why_picked: "Minimises supply disruption while closing financial exposure on a defensible timeline."

# === Renderer routing ===
compatible_renderers:
  - dark-deck-report
narrative_template: investigation

# === Citation register ===
citation_register: forensic

# === Color semantics ===
color_semantics: default

# === Source linkage ===
evidence_file: ./evidence.md
sources_dir: ./sources
---

# Slides

## Slide 1 — Cover

```yaml
role: cover
action_title: "Q3 supplier audit identifies $4.2M exposure across 12 vendors"
hero_pattern: null
evidence_refs: []
quality:
  title_score: 92
  ghost_deck_pass: true
  rewrite_attempts: 0
```

**Speaker notes**: Set context fast — audience already knows the audit scope.

## Slide 2 — TL;DR

```yaml
role: tldr
action_title: "Three findings, two recommendations, one decision needed by 2026-06-15"
bullets:
  - "26% of audited vendors failed contractual control checks — $4.2M exposure"
  - "Root cause: 2024 re-papering exercise missed renewals on 12 master agreements"
  - "Three-step corrective plan closes exposure in 90 days without supply disruption"
evidence_refs: [E1, E2, E3]
quality:
  title_score: 88
  ghost_deck_pass: true
  rewrite_attempts: 0
```

**Speaker notes**: This slide alone answers the board's question if time runs out.

## Slide 3 — Context

```yaml
role: context
action_title: "47 vendors, $182M spend, 90-day audit window — scope per board charter"
bullets:
  - "Audit charter dated 2025-06-12, approved at board meeting M-2025-06"
  - "Scope: all tier-1 + tier-2 vendors with FY25 spend ≥ $500K"
  - "Fieldwork ran 2025-07-15 through 2025-10-15"
evidence_refs: [E4]
quality:
  title_score: 90
  ghost_deck_pass: true
  rewrite_attempts: 0
```

**Speaker notes**: Establish bounds — pre-empt "did you cover X?" questions.

## Slide 5 — Hero

```yaml
role: hero
action_title: "Exposure concentrates in 4 of 12 failing vendors — 71% of $4.2M"
hero_pattern: matrix-2x2
bullets: []
data_callouts:
  - { label: "Vendor A", value: "$1.4M", x: 92, y: 18 }
  - { label: "Vendor B", value: "$0.9M", x: 78, y: 22 }
  - { label: "Vendor C", value: "$0.5M", x: 65, y: 31 }
  - { label: "Vendor D", value: "$0.2M", x: 71, y: 45 }
evidence_refs: [E5, E6]
quality:
  title_score: 94
  ghost_deck_pass: true
  rewrite_attempts: 0
```

**Speaker notes**: 2×2 axes = exposure ($) vs days-since-control-failure. Top-right quadrant is the priority list.

## Slide 13 — Recommendation

```yaml
role: recommendation
action_title: "Adopt 3-step corrective plan: re-paper (30d), escrow (60d), sunset (90d)"
bullets:
  - "Step 1 (by 2026-06-15): Re-paper the 12 vendors with current master template"
  - "Step 2 (by 2026-07-15): Move disputed billing to escrow pending re-paper completion"
  - "Step 3 (by 2026-08-15): Sunset 4 vendors that decline re-paper; activate backup suppliers"
evidence_refs: [E7, E8]
quality:
  title_score: 96
  ghost_deck_pass: true
  rewrite_attempts: 0
```

**Speaker notes**: This is the ask. Board decision triggers Step 1 immediately.

## Slide 14 — Appendix (Methodology)

```yaml
role: appendix
action_title: "Methodology: 47 vendors sampled per AICPA AT-C 105, chain-of-custody preserved"
bullets:
  - "Sampling frame: vendor master file as of 2025-06-30"
  - "Control tests: 8 contractual + 4 operational per vendor"
  - "Evidence chain-of-custody: all exhibits SHA-256 hashed, logged in audit-trail.md"
evidence_refs: [E9]
quality:
  title_score: 85
  ghost_deck_pass: true
  rewrite_attempts: 0
```

**Speaker notes**: Skip unless asked; here to defend the rigor of findings if challenged.

<!--
This template is illustrative. Real outlines may have 4 slides (depth=brief),
14 slides (depth=full), or 1 slide (depth=minimal). The renderer picks the chassis
based on `delivery.depth`. Slides not used for the chosen depth are silently dropped
unless marked `pin: true` (not in v0.1; possibly v0.2).
-->
