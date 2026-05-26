# Report Types · Arc Adaptations

The 14-slide chassis is universal; the **role** each slide plays adapts to the report type. This doc maps subject → which slides matter, which compress, and which hero pattern to pick.

---

## Quick selector

| Subject | Hero (S5) | Emphasis | Compress / skip |
|---------|-----------|----------|-----------------|
| Architecture brief | Pipeline / Stack | 5, 6, 7, 8, 9, 12 | 10 if formulas are minor |
| Research summary | Stack / Quadrant | 3, 7 (findings), 10 (model), 11 (refs dense) | 12, 13 |
| Postmortem / incident review | Timeline | 3 (impact), 5 (timeline), 6 (RCA), 14 (action items) | 12 |
| Strategy / business case | Quadrant / Funnel | 2 (TL;DR), 4 (thesis), 5 (positioning), 13 (plan) | 10 |
| Technical RFC | Stack / Comparison | 4 (proposal), 5 (design), 7 (tradeoffs), 12 (alternatives) | 11 (use inline cites instead) |
| Project pitch / fundraising | Funnel / Hub-spoke | 1 (cover impact), 2, 4, 13 (the ask) | 10 |
| Due-diligence memo | Comparison / Quadrant | 3 (problem statement), 7, 10 (financials), 12 (options) | 5, 13 |
| Design review | Hub-spoke / Stack | 5 (system), 7 (key decision), 12 (alternatives considered) | 10, 13 |
| Quarterly review | Funnel / Comparison | 3 (where we missed), 10 (KPIs), 13 (next quarter) | 5, 11 |
| Threat model / security review | Hub-spoke / Quadrant | 3 (assets), 5 (attack surface), 7 (mitigations), 10 (risk scoring) | 13 |

---

## Architecture brief

> "Here's the system we're proposing, why it solves the problem, what tools we'll use, and what we're asking you to approve."

**Anchor slides:** 4 (thesis), 5 (architecture diagram), 6–9 (stages/branches), 12 (tool selection).

**Hero pattern:** **Pipeline** if process-flow; **Stack** if layered system.

**Special considerations:**
- Slide 10 (quantitative model) often becomes "Performance budget" or "Capacity planning" — formulas optional, throughput/latency tables more common.
- Slide 13 (roadmap) should match Phase 0 / Phase 1 / Phase 2 to specific deliverables and gates.
- Slide 11 needs a balance of commercial precedents (vendor systems doing this in production) and academic foundation (papers backing the technical choices).

**Watch-out:** Don't let slide 5 become a brand-name shopping list. The hero shows the *structure*; tool names live in slide 12.

---

## Research summary

> "Here's what we found, the methodology behind it, and what it means for our decisions."

**Anchor slides:** 3 (research question + scope), 7 (methodology), 10 (results / model), 11 (literature anchored deeply).

**Hero pattern:** **Stack** if framework-of-thought; **Quadrant** if positioning vs prior work.

**Adaptations:**
- Slide 5 hero can become a methodology diagram or a results-at-a-glance visualization rather than architecture.
- Slide 10 carries more weight than usual — this is where results live. Tables, charts, formulas, confidence intervals.
- Slide 11 (refs) becomes *dense* — research reports lean on 12–20 references, not 8. Tighten the per-slide compression in `#s11` accordingly.
- Slide 12 (selection matrix) often becomes "methods considered + chosen" rather than tools.
- Slide 13 (roadmap) usually skipped or replaced with "follow-up questions / open threads."

---

## Postmortem / incident review

> "What happened, why it happened, what damage it did, what we'll do to prevent recurrence."

**Anchor slides:** 3 (impact: who, how much, how long), 5 (incident timeline), 6 (root cause), 14 (action items with owners + dates).

**Hero pattern:** **Timeline** — always. The chronology is the spine of the document.

**Adaptations:**
- Slide 4 (thesis) becomes "TL;DR of root cause + remediation."
- Slide 5 (hero) is the incident timeline with timestamps, severity markers, and decision points.
- Slide 6 becomes contributing factors / 5-whys analysis.
- Slide 7 becomes "what we did right" + "what we did wrong" — preserve learnings.
- Slide 10 becomes blast-radius math (rows affected, customers impacted, revenue lost).
- Slide 12 (selection matrix) becomes action items table with owner, date, gate.
- Slide 14 must list **specific** owners + dates, never "the team will fix this."

**Tone:** Blameless. Factual. No defensive language. The amber colour should appear when describing "contributing factors that, if any one were different, would have prevented the incident."

---

## Strategy / business case

> "Here's the market opportunity, our proposed positioning, why now, and what we're asking for."

**Anchor slides:** 1 (cover impact), 2 (TL;DR), 4 (thesis), 5 (positioning), 13 (the plan).

**Hero pattern:** **Quadrant** for positioning; **Funnel** for opportunity sizing.

**Adaptations:**
- Slide 3 becomes "market context" with TAM/SAM/SOM or competitor analysis.
- Slide 5 (hero) is the positioning matrix or strategic option-space.
- Slide 7 becomes "differentiation" — why us, not them.
- Slide 10 becomes financial model with revenue/cost projections.
- Slide 11 (refs) often becomes "Sources" — market research reports, analyst quotes, customer interviews. Same citation rigour applies.
- Slide 13 (roadmap) is **the ask** — 30/60/90 plan with explicit resource asks.

**Tone:** Confident but defensible. Numbers come from named sources. Avoid "we believe" — write "X analyst report shows..."

---

## Technical RFC

> "Here's a design proposal for review. Here are the tradeoffs. Here's why I want you to accept it."

**Anchor slides:** 4 (proposal), 5 (design), 7 (tradeoffs / alternatives considered), 12 (technology choices).

**Hero pattern:** **Stack** for system layering; **Comparison** if RFC is "us vs alternatives."

**Adaptations:**
- Slide 2 becomes "summary of proposal + decisions sought."
- Slide 4 is the proposal — exactly what's being asked for.
- Slide 5 (hero) is the design itself — interfaces, components, contracts.
- Slide 7 carries unusual weight: alternatives considered AND rejected, with reasons. This is the credibility move.
- Slide 11 (refs) can use inline citations within other slides instead of a dedicated references page — RFCs are often technical with fewer formal citations.
- Slide 14 should explicitly name reviewers and gating questions.

**Tone:** Open about uncertainty. List unknowns. Identify decisions to defer.

---

## Project pitch / fundraising

> "Here's the problem, here's our solution, here's the team and traction, here's the ask."

**Anchor slides:** 1 (cover with impact), 2 (TL;DR), 4 (proposal), 13 (the ask).

**Hero pattern:** **Funnel** for market sizing; **Hub-spoke** for product capabilities.

**Adaptations:**
- Slide 1 (cover) gets extra design attention — bigger ornament, longer lede, optional photo overlay.
- Slide 3 becomes "the problem" — customer pain quantified, often with quotes.
- Slide 4 becomes "the product" — what it does, how it works.
- Slides 6–9 become product / traction / team / business model.
- Slide 10 becomes unit economics (CAC, LTV, payback period).
- Slide 11 becomes "social proof" — investors, customers, advisors, press.
- Slide 13 IS the ask — round size, valuation, use of funds.

**Tone:** Confident, specific, no vapor. Numbers must be defensible if asked.

---

## Due-diligence memo

> "We evaluated X. Here's what we found. Here's our recommendation."

**Anchor slides:** 3 (target overview), 7 (deep-dive findings), 10 (financials), 12 (red/yellow/green scorecard).

**Hero pattern:** **Comparison** if vs. peers; **Quadrant** if scoring on multiple dimensions.

**Adaptations:**
- Slide 2 becomes "summary verdict" — proceed / conditional / decline.
- Slide 5 (hero) is the scoring matrix.
- Slide 10 is the financials slide with multiple tables.
- Slide 11 becomes "sources consulted" — interviews, public filings, expert calls.
- Slide 12 becomes the issue-list table (each item: severity, mitigation, decision impact).
- Slide 13 often skipped or replaced with "conditions precedent."

---

## Design review

> "Here's the design. Here are the decisions we made and why. Tell us what to change."

**Anchor slides:** 5 (the design), 7 (key tradeoffs), 12 (alternatives considered).

**Hero pattern:** **Hub-spoke** for design surface area; **Stack** for layered design.

**Adaptations:**
- Slide 4 becomes design principles / constraints.
- Slide 5 (hero) is the design artifact — wireframe, system diagram, API surface.
- Slides 6–9 are design deep-dives on each major surface.
- Slide 10 becomes accessibility / performance / quality criteria.
- Slide 12 becomes alternatives explored.
- Slide 14 becomes "open questions for reviewers."

---

## Quarterly review

> "Here's what we committed, here's what we did, here's what we missed, here's next quarter."

**Anchor slides:** 3 (commitments vs delivery), 10 (KPI dashboard), 13 (next quarter plan).

**Hero pattern:** **Funnel** for delivery rate; **Comparison** for vs-plan view.

**Adaptations:**
- Slide 2 becomes "the headline" — beat / met / missed.
- Slide 3 becomes "what we said we'd do."
- Slide 4 becomes "what we delivered."
- Slide 5 (hero) is the KPI dashboard or vs-plan comparison.
- Slides 6–9 are deep-dives on the biggest beats and biggest misses.
- Slide 10 is detail KPIs with deltas.
- Slide 11 often skipped.
- Slide 12 becomes "what we'll change."
- Slide 13 is next quarter.

**Tone:** Honest. Misses get named with reasons. Don't hide behind aggregate metrics.

---

## Threat model / security review

> "Here are the assets, here's the attack surface, here are the mitigations and residual risk."

**Anchor slides:** 3 (assets + criticality), 5 (attack surface), 7 (mitigations), 10 (risk scoring).

**Hero pattern:** **Hub-spoke** for asset/attack-vector map; **Quadrant** for risk × impact scoring.

**Adaptations:**
- Slide 3 becomes asset inventory with crown-jewel highlighting.
- Slide 5 (hero) is the attack surface diagram.
- Slide 6–9 become specific threats with STRIDE / kill-chain analysis.
- Slide 10 is the risk matrix (likelihood × impact).
- Slide 11 cites standards: NIST, ISO 27001, OWASP Top 10, ATT&CK.
- Slide 12 becomes mitigation roadmap with priority + residual risk.

---

## Picking your variant

If you're unsure which report type your work matches, ask:

1. **What's the reader's single question?**
   - "What should we build?" → Architecture brief / RFC
   - "What did we learn?" → Research / Postmortem
   - "What should we do as a business?" → Strategy / Pitch
   - "How did we do?" → Quarterly review
   - "Is this safe / valuable?" → Threat model / Due diligence

2. **What's the centerpiece visual?** (drives the hero pattern choice)

3. **What's the slide-1 contract?** What do you want the reader to expect from pages 2–14?

Once those three are answered, the slide arc adapts cleanly.
