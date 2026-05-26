---
schema_version: 0.1
---

# E1
source: "Internal audit report, 2025-09-15.pdf, p.12"
type: document
verifiable: true
quote: "Of 47 vendors in scope, 12 (26%) had no current master agreement on file."
context: "Section 3.2, finding F-04"
provenance: "sources/audit-report-2025-09-15.pdf"

# E2
source: "Supplier contract clause 4.2(b), executed 2024-03-01"
type: document
verifiable: true
quote: "Either party may withhold payment for invoices not supported by an active master agreement."
context: "Standard MSA template, §4.2(b)"
provenance: "sources/msa-template-v2024.pdf"

# E3
source: "Vendor billing reconciliation, FY25 Q3"
type: dataset
verifiable: true
context: "Computed $4.2M as sum of disputed-status invoices across the 12 affected vendors"
provenance: "sources/billing-recon-fy25q3.xlsx"

# E4
source: "Board charter for compliance audit program, dated 2025-06-12"
type: document
verifiable: true
context: "Approved at board meeting M-2025-06, §3 defines tier-1 / tier-2 scope"
provenance: "sources/board-charter-2025-06-12.pdf"

# E5
source: "Vendor exposure analysis, attached as Exhibit A"
type: dataset
verifiable: true
context: "Pareto distribution; 4 vendors account for $3.0M of $4.2M total"
provenance: "sources/exhibit-a-exposure-analysis.xlsx"

# E6
source: "Interview with VP Procurement, 2025-10-08"
type: interview
verifiable: true
note: "On record; full transcript at sources/interviews/2025-10-08-vp-proc.md"
context: "Confirmed root cause: 2024 re-papering exercise missed renewals"

# E7
source: "Legal opinion on escrow mechanism, 2026-01-22"
type: document
verifiable: true
quote: "Escrow is permitted under all 12 MSAs subject to 30-day notice."
context: "Opinion from outside counsel, Smith & Reed"
provenance: "sources/legal-opinion-escrow-2026-01-22.pdf"

# E8
source: "Supply continuity risk assessment, 2026-02-10"
type: document
verifiable: true
context: "Identified backup suppliers for 4 sunset-candidate vendors"
provenance: "sources/supply-continuity-2026-02-10.pdf"

# E9
source: "AICPA Attestation Standard AT-C section 105"
type: paper
verifiable: true
context: "Sampling methodology basis"
