# Schema: RAID Log (`raid.md`)

Risks, Assumptions, Issues, Dependencies. One file, four record types, shared conventions from `program-state.md` §2.

Definitions:
- **Risk** — uncertain future event that would harm an outcome or commitment.
- **Assumption** — something the plan relies on that has not been verified.
- **Issue** — a risk that has materialized; it is harming the program now.
- **Dependency** — something the program needs from a party it does not control.

---

## Common fields (all types)

```yaml
id: RISK-007                # RISK- | ASM- | ISS- | DEP-
type: risk                  # risk | assumption | issue | dependency
title: "..."
status: open                # see per-type statuses
raised_on: 2026-09-02
source: "<evidence ref>"
owner: { name: "...", confirmation: proposed }
affects: [MS-003, OUT-001]  # milestones, outcomes, commitments
links: []
history: []
```

## Risk (`RISK-`)

```yaml
status: open                 # open | mitigating | accepted | materialized | closed | superseded
likelihood: medium           # low | medium | high  (with basis)
likelihood_basis: "..."
impact: high                 # low | medium | high
impact_statement: "<concrete: X days on MS-003, $Y, client SLA breach>"
time_to_impact: 2026-10-10   # date by which it would hit if unmitigated
trigger: "<observable early-warning signal>"
mitigation: "<action that reduces likelihood or impact>"
mitigation_owner: { name: "...", confirmation: proposed }
contingency: "<what we do if it materializes>"
materialized_as: null        # ISS- id when it becomes an issue
```

## Assumption (`ASM-`)

```yaml
status: unverified           # unverified | verified | invalidated | superseded
if_wrong: "<consequence>"
verification: "<how to verify>"
verify_by: 2026-09-25
verified_on: null
```

An invalidated assumption must spawn a RISK or ISS record in the same review.

## Issue (`ISS-`)

```yaml
status: open                 # open | resolving | resolved | closed
from_risk: RISK-003          # null if not anticipated — which is itself a signal
impact_now: "..."
resolution_plan: "..."
resolve_by: 2026-09-30
root_cause: "<mechanism, or 'unknown — to find: X'>"
recurrence_of: null          # earlier ISS id if this has happened before
```

## Dependency (`DEP-`)

```yaml
status: open                 # open | at_risk | delivered | broken | removed
provider: { team_or_party: "...", counterpart_owner: { name: "...", confirmation: unknown } }
what: "<the deliverable, API, environment, approval, data>"
interface_contract: "<link or 'none'>"   # 'none' is a finding
needed_by: 2026-10-01
promised_by: null            # null = no promise on record
promise_source: null
on_critical_path: true
fallback: "<what we do if it does not arrive, or 'none'>"
elimination_option: "<design change that removes this dependency, or 'none identified'>"
```

## Derived signals (computed by reviews)

- **Unowned dependency:** `provider.counterpart_owner.confirmation != confirmed`.
- **Unpromised dependency:** `promised_by == null` or `promised_by > needed_by`.
- **Aging:** open for ≥2 consecutive reviews with no `history` entry.
- **Surprise issue:** `ISS` with `from_risk: null`.
- **Recurring issue:** `recurrence_of != null`.

## Rules

- A risk without `trigger` and `time_to_impact` is incomplete; the review must propose completing it.
- A critical-path dependency with no counterpart owner, no promise, or no fallback caps the `dependencies` dimension at `red`.
