# RAID — Billing Integration

Schema: principal-pm-agent/schemas/raid.md · Append-only · Program: PGM-billing-integration

### RISK-001 — Category mapping decision delays go-live

```yaml
id: RISK-001
type: risk
title: "Category mapping decision delays go-live"
status: open
raised_on: 2026-08-19
source: REV-2026-08-19
owner: { name: "Principal", confirmation: confirmed }
affects: [MS-001, COM-001]
likelihood: medium
likelihood_basis: "teams disagree; no decision rights defined"
impact: high
impact_statement: "Each week of delay pushes 21 items past go-live"
time_to_impact: 2026-09-01
trigger: "DEC-002 not decided by 2026-08-29"
mitigation: "Get leads to agree"
mitigation_owner: { name: "Integration lead", confirmation: proposed }
contingency: null
materialized_as: null
links: [DEC-002]
history:
  - { on: 2026-09-02, change: "trigger hit (deadline passed); status unchanged", by: agent-proposed-approved, source: REV-2026-09-02 }
```

### DEP-001 — Partner webhook payload v2 spec

```yaml
id: DEP-001
type: dependency
title: "Partner webhook payload v2 spec"
status: open
raised_on: 2026-08-12
source: "evidence/2026-08-12/sync-notes.md"
owner: { name: "Integration lead", confirmation: confirmed }
affects: [MS-001]
provider: { team_or_party: "External partner", counterpart_owner: { name: null, confirmation: unknown } }
what: "Webhook payload v2 spec for invoice events (CAT-6..CAT-9)"
interface_contract: none
needed_by: 2026-09-15
promised_by: null
promise_source: null
on_critical_path: true
fallback: none
elimination_option: "none identified"
links: [DEC-002]
history: []
```
