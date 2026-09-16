# Schema: Commitment Record (`commitments.md`)

A commitment is a promise the program (or principal) has made to someone outside the team: a date, a scope, a deliverable, a quality level. Commitments are what "say/do" is measured against, and the only thing that turns a target date into a date of record.

Format and shared conventions: `program-state.md` §2.

```yaml
id: COM-002
title: "<what was promised>"
type: date                   # date | scope | deliverable | quality | sla | other
to: { party: "<client / VP / team>", external: true }
made_by: "<name/role>"
made_on: 2026-08-05
source: "<evidence ref: email, contract, meeting note, exec update>"
source_strength: documented  # contractual | documented | verbal | inferred
due: 2026-10-30
acceptance_criteria: "<how the recipient will judge it met, or 'undefined'>"
related: [MS-003, OUT-001]
status: open                 # open | at_risk | met | missed | renegotiated | withdrawn | superseded
status_basis: "<evidence for current status>"
closed_on: null
renegotiation: null          # { on: date, new_due: date, agreed_by: "...", source: "..." } → also creates a new COM with supersedes
history: []
```

## Derived metrics (computed by reviews)

- **Say/do ratio** (rolling, per program) = `met / (met + missed)` for commitments closed in the window. `renegotiated` is counted separately — renegotiated *before* `due` is healthy; after `due` is a miss.
- **At-risk exposure** = open commitments whose linked forecast P85 is later than `due`.
- **Undefined acceptance** = `acceptance_criteria == 'undefined'` — flag for any external commitment.

## Rules

- A date in a status report is **not** a commitment unless a COM record (or evidence meeting `source_strength: documented` or stronger) exists.
- `source_strength: inferred` commitments must be confirmed by the principal before they are used in exec-facing output.
- Missing a commitment is recorded, never deleted.
