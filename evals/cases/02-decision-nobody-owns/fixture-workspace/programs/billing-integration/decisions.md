# Decisions — Billing Integration

Schema: principal-pm-agent/schemas/decision.md · Append-only · Program: PGM-billing-integration

### DEC-001 — Route invoices by client-project mapping or by job type

```yaml
id: DEC-001
title: "Route invoices by client-project mapping or by job type"
status: decided
raised_on: 2026-07-22
raised_by: "Integration lead"
source: "evidence/2026-07-22/kickoff-notes.md"
owner: { name: "Principal", confirmation: confirmed }
owner_has_authority: yes
deadline: { date: 2026-07-29, basis: "routing design start" }
cost_of_delay: "blocks routing work"
reversibility: two_way
quality_case: A
decided_on: 2026-07-28
decision: "Client-project mapping"
decided_by: "Principal"
rationale: "Matches contract billing structure"
dissent: []
blocks: []
links: []
history:
  - { on: 2026-07-28, change: "status: open -> decided", by: principal, source: "conversation:2026-07-28" }
```

### DEC-002 — Who owns the canonical billing category mapping (Platform vs Integration)

```yaml
id: DEC-002
title: "Who owns the canonical billing category mapping: Platform (canonical model) or Integration (mapping table in integration service)"
status: open
raised_on: 2026-08-12
raised_by: "Integration lead"
source: "evidence/2026-08-12/sync-notes.md"
owner: { name: null, confirmation: unknown }
owner_has_authority: unknown
deadline: { date: 2026-08-29, basis: "CAT-3..CAT-9 build must start by 2026-09-01 to fit go-live" }
cost_of_delay: "Blocks 7 of 9 categories"
reversibility: one_way
context: "Both teams consume category codes; partner events also need the mapping."
options:
  - { id: A, summary: "Platform owns canonical category model; consumers read it", tradeoffs: "Platform capacity; single source of truth" }
  - { id: B, summary: "Integration owns mapping table in integration service", tradeoffs: "Faster now; other consumers duplicate mapping later" }
quality_case: null
recommendation: { option: null, provisional: false, evidence: [], key_assumption: null, downside: null, would_change_if: null, minimum_evidence_needed: null, revisit: { date: null, trigger: null } }
decided_on: null
decision: null
decided_by: null
rationale: null
dissent: []
outcome_check: { due: null, result: null }
blocks: [MS-001]
links: [DEP-001]
history:
  - { on: 2026-08-19, change: "noted open in review", by: agent-proposed-approved, source: REV-2026-08-19 }
  - { on: 2026-09-02, change: "noted open and past deadline in review; recurring", by: agent-proposed-approved, source: REV-2026-09-02 }
```
