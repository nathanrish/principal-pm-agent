---
program_id: PGM-billing-integration
name: "Billing Integration"
charter_version: 1
charter_updated: 2026-07-18
accountable_owner: { name: "Principal", confirmation: confirmed }
sponsor: { name: "Delivery Director", confirmation: confirmed }
problem: "Client B's invoices are assembled manually from job data across 9 billing categories, causing billing errors and a 6-day average time-to-invoice."
why_now: "Client B contract amendment requires automated category-level billing from 2026-10-15; manual process cost and error disputes are rising."
core_bet: "A single canonical billing category model can serve routing, invoicing, and partner events."
outcomes:
  - id: OUT-001
    statement: "Client B invoices generated automatically with correct category-level billing."
    metric_id: invoice_category_error_rate
    baseline: { value: 0.07, as_of: 2026-07-01, source: "finance dispute log, Q2" }
    target: { value: 0.01, by: 2026-12-31 }
  - id: OUT-002
    statement: "Reduce time-to-invoice for Client B."
    metric_id: time_to_invoice_days
    baseline: { value: 6, as_of: 2026-07-01, source: "finance ops report" }
    target: { value: 1, by: 2026-12-31 }
non_goals: ["Other clients' billing", "Tax engine changes"]
hard_constraints:
  - { type: contractual, statement: "Automated category-level billing live for Client B by 2026-10-15", source: "COM-001" }
clients_or_stakeholders:
  - { name: "Client B", role: "customer", exposure: "contractual go-live" }
kill_or_pivot_criteria: ["If canonical model cannot cover all 9 categories, fall back to per-category adapters and renegotiate scope"]
---
