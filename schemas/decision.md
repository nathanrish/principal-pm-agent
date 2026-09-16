# Schema: Decision Record (`decisions.md`)

Decisions are the scarcest resource in a program. This record exists to make decision latency, ownership, and outcomes visible over time.

Format and shared conventions: `program-state.md` §2.

```yaml
id: DEC-003
title: "<the question, phrased as a choice>"
status: open            # open | recommended | decided | superseded | reversed | withdrawn
raised_on: 2026-08-20
raised_by: "<name/role>"
source: "<evidence path or conversation:date>"

owner: { name: "<name/role>", confirmation: proposed }   # confirmed | proposed | unknown
owner_has_authority: unknown                              # yes | no | unknown
deadline: { date: 2026-09-20, basis: "<why this date — e.g. last responsible moment for MS-003>" }
cost_of_delay: "<concrete: days of critical-path time, money, client exposure>"
reversibility: two_way  # one_way | two_way | unknown

context: "<why this decision is needed now>"
options:
  - id: A
    summary: "..."
    tradeoffs: "..."
  - id: B
    summary: "..."
    tradeoffs: "..."
  - id: C
    summary: "Do nothing / defer"
    tradeoffs: "..."

quality_case: A          # A (recommend) | B (insufficient, waiting cheap) | C (insufficient, waiting expensive) — see CLAUDE.md §5.3
recommendation:
  option: A              # null for case B
  provisional: false     # true for case C
  evidence: ["<refs>"]
  key_assumption: "..."
  downside: "..."
  would_change_if: "..."
  minimum_evidence_needed: null   # required for case B and C
  revisit: { date: null, trigger: null }  # required for case C

decided_on: null
decision: null           # chosen option id + summary
decided_by: null
rationale: null
dissent: []              # [{ by: "...", concern: "...", reopen_trigger: "..." }]
outcome_check: { due: null, result: null }   # was the decision right? filled later

blocks: [MS-003]         # milestones/records this decision gates
links: [DEP-004, RISK-007]
history: []
```

## Derived metrics (computed by reviews)

- **Decision latency** = `decided_on − raised_on` (closed) or `today − raised_on` (open).
- **Overdue** = `status in [open, recommended]` and `today > deadline.date`.
- **Unowned** = `owner.confirmation != confirmed`.
- **Recurring** = same `id` appears as open and overdue in ≥2 consecutive reviews.

## Rules

- A decision with `owner.confirmation: unknown` or no `deadline` cannot be rated better than `amber` on `decision_latency`.
- "We'll align next week" is not a decision, an owner, or a deadline. Record it as `history` on an `open` decision.
- Never set `status: decided` without a `decided_by` and a `source`.
