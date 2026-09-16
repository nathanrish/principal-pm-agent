# Schema: Workspace, Program State, and Review Records

This is the root schema. It defines the workspace layout, the conventions every record type shares, and the formats for `charter.md`, `state.md`, and review records. Record-specific schemas: `decision.md`, `raid.md`, `commitment.md`, `forecast-log.md`, `metrics-definitions.md`.

**Design goal:** any two reviews of the same program, months apart, can be compared mechanically — same dimension IDs, same enums, same metric definitions (or an explicit version change), stable record IDs.

---

## 1. Workspace layout

```
$PM_WORKSPACE/
├── org.md                         # team, roles, decision rights, cadence, tools
├── metrics.md                     # optional: org metric overrides (schema: metrics-definitions.md)
└── programs/<program-id>/
    ├── charter.md                 # stable frame; changes rarely, versioned
    ├── state.md                   # CURRENT view only; derived from approved records
    ├── decisions.md               # DEC records (append-only)
    ├── raid.md                    # RISK / ASM / ISS / DEP records (append-only)
    ├── commitments.md             # COM records (append-only)
    ├── forecasts.md               # FC records (append-only)
    ├── evidence/
    │   └── YYYY-MM-DD/            # one folder per evidence drop
    │       ├── MANIFEST.md        # required: what each file is, source, exported_at, period
    │       └── <files>            # CSV exports, notes, status reports, docs
    └── reviews/
        └── YYYY-MM-DD[-quick].md  # approved review records (immutable once written)
```

The workspace should be a private git repository. Git history is the audit trail; the append-only conventions make diffs meaningful.

---

## 2. Shared conventions

### 2.1 IDs

| Prefix | Record | File |
|---|---|---|
| `MS-` | Milestone | `state.md` |
| `OUT-` | Outcome objective | `charter.md` |
| `DEC-` | Decision | `decisions.md` |
| `RISK-` | Risk | `raid.md` |
| `ASM-` | Assumption | `raid.md` |
| `ISS-` | Issue | `raid.md` |
| `DEP-` | Dependency | `raid.md` |
| `COM-` | Commitment | `commitments.md` |
| `FC-` | Forecast | `forecasts.md` |
| `REV-` | Review record | `reviews/` |
| `CHG-` | Proposed change (scoped to one review/init output) | review record `approved_changes` |
| `INT-` | Intervention (scoped to one review) | review record `interventions` |

- Format: `PREFIX-NNN`, zero-padded, sequential per program. `REV-` uses the date: `REV-2026-09-16` (suffix `-q` for quick reviews).
- IDs are never reused, even after a record is closed or superseded.
- Cross-reference by ID everywhere (e.g., `blocked_by: [DEP-004]`).

### 2.2 Record block format

Every record in an append-only file is a level-3 heading followed by one fenced YAML block:

````
### RISK-007 — Client UAT environment not provisioned

```yaml
id: RISK-007
...
```

Optional free-text notes below the block.
````

One record per heading. Tools and the agent parse the YAML; humans read the heading.

### 2.3 History (append-only)

- Never delete a record. Never edit a past `history` entry.
- Every change appends to the record's `history` list:
  ```yaml
  history:
    - on: 2026-09-16
      change: "status: open -> mitigating"
      by: principal            # principal | agent-proposed-approved | <name>
      source: REV-2026-09-16   # review ID, evidence path, or conversation
  ```
- Current fields (e.g., `status`) are updated in place **and** the change is recorded in `history`.
- Replacing a record: set `status: superseded`, `superseded_by: <new ID>`; the new record sets `supersedes: <old ID>`.

### 2.4 Shared enums

| Field | Allowed values |
|---|---|
| `rating` (RAG) | `green` · `amber` · `red` · `no_evidence` |
| `trend` | `improving` · `stable` · `worsening` · `new` (no prior rating) · `unknown` |
| `confidence` | `high` · `medium` · `low` |
| `evidence_grade` | `A` system-of-record export ≤7 days old · `B` system-of-record 8–21 days, or owner-authored doc · `C` narrative/second-hand/meeting notes, or any evidence >21 days · `none` |
| `claim_type` | `fact` · `claim` · `assumption` · `inference` |
| `confirmation` (owners, dates) | `confirmed` · `proposed` · `unknown` |

### 2.5 Dates and evidence references

- Dates: ISO `YYYY-MM-DD`. Every record carries `as_of` or `on` where time matters.
- Evidence references: relative path + locator, e.g. `evidence/2026-09-15/workitems.csv#rows:12-40` or `evidence/2026-09-15/status-note.md#"UAT confirmed"`.
- A reference to a conversation: `conversation:YYYY-MM-DD`.

---

## 3. `charter.md`

Frontmatter (YAML), then optional prose.

```yaml
program_id: PGM-slug              # stable, lowercase slug after PGM-
name: "<Program name>"
charter_version: 1                # increment on any change to outcomes, scope, constraints
charter_updated: 2026-09-16
accountable_owner: { name: "<name or role>", confirmation: confirmed }
sponsor: { name: "<name or role>", confirmation: proposed }
problem: "<who has what problem, in one or two sentences>"
why_now: "<cost of delay>"
core_bet: "<riskiest assumption the program rests on>"
outcomes:
  - id: OUT-001
    statement: "<the change in user/business behavior>"
    metric_id: <metric id from metrics definitions>
    baseline: { value: null, as_of: null, source: null }   # null = unmeasured, must be flagged
    target: { value: null, by: null }
non_goals: ["..."]
hard_constraints:
  - { type: contractual|date|budget|regulatory|technical, statement: "...", source: "..." }
clients_or_stakeholders:
  - { name: "<redacted or real>", role: "...", exposure: "..." }
kill_or_pivot_criteria: ["..."]
```

---

## 4. `state.md` — current view

`state.md` is a **derived snapshot** of approved records. It is overwritten (via approved CHG) after each approved review. History does **not** live here; it lives in `reviews/` and the append-only logs.

```yaml
program_id: PGM-slug
as_of: 2026-09-16
last_review: REV-2026-09-16
charter_version: 1
overall: { rating: amber, trend: worsening, confidence: medium }
phase: "<discovery|build|pilot|launch|operate|...>"
milestones:
  - id: MS-003
    name: "<milestone>"
    committed_date: 2026-10-30      # null if never committed
    commitment_ref: COM-002          # required if committed_date is set
    current_forecast_ref: FC-005     # null if no defensible forecast
    status: not_started|in_progress|done|dropped
    on_critical_path: true
scope:
  baseline_item_count: 58
  baseline_as_of: 2026-08-01
  current_item_count: 71
  current_as_of: 2026-09-15
  source: "evidence/2026-09-15/workitems.csv"
open_records:
  decisions: [DEC-003, DEC-004]
  risks: [RISK-002, RISK-007]
  issues: [ISS-001]
  dependencies: [DEP-004]
  commitments_due_next_30d: [COM-002]
  forecasts_open: [FC-005]
```

---

## 5. Review record (`reviews/YYYY-MM-DD[-quick].md`)

A review record is **immutable once written**. It consists of machine-readable frontmatter followed by the full human-readable review output. The frontmatter is what future reviews compare against.

```yaml
review_id: REV-2026-09-16            # REV-2026-09-16-q for quick mode
program_id: PGM-slug
mode: full|quick
reviewed_on: 2026-09-16
previous_review: REV-2026-09-02      # null for first review
engine_version: "<git short SHA of engine repo, or 'unknown'>"
charter_version: 1
metrics_definitions_version: "<engine SHA or workspace metrics.md version>"
evidence:
  overall_grade: B                   # worst grade among evidence used for Red/Green calls
  items:
    - { path: "evidence/2026-09-15/workitems.csv", source: "work tracker export", as_of: 2026-09-15, grade: A }
  gaps: ["<missing evidence that blocked a rating>"]
  conflicts: ["<source X says A; source Y says B>"]
overall: { rating: amber, trend: worsening, confidence: medium }
scorecard:                           # all 12 dimension IDs must be present, every review, every mode
  outcomes:        { rating: no_evidence, trend: unknown, evidence: ["..."] }
  forecast:        { rating: red,   trend: worsening, evidence: ["FC-005"] }
  scope:           { rating: amber, trend: worsening, evidence: ["..."] }
  dependencies:    { rating: red,   trend: stable,    evidence: ["DEP-004"] }
  risks:           { rating: amber, trend: new,       evidence: ["RISK-007"] }
  decision_latency: { rating: red, trend: worsening, evidence: ["DEC-003"] }
  quality:         { rating: no_evidence, trend: unknown, evidence: [] }
  architecture:    { rating: no_evidence, trend: unknown, evidence: [] }
  ownership:       { rating: amber, trend: stable,    evidence: ["..."] }
  adoption:        { rating: no_evidence, trend: unknown, evidence: [] }
  economics:       { rating: no_evidence, trend: unknown, evidence: [] }
  client_exposure: { rating: amber, trend: new,       evidence: ["COM-002"] }
metrics:                             # every metric value cited in the review
  - { metric_id: cycle_time_p85, definition_version: 1, value: 18, unit: days, as_of: 2026-09-15, source: "..." }
forecast_ref: FC-005                 # null if no defensible forecast
stated_status: { rating: green, source: "evidence/2026-09-15/status-note.md" }   # what the team/status report claimed; null if none
watermelon_flag: true                # stated_status better than evidence-based overall
recurring_issues: ["<issue seen in ≥2 consecutive reviews, with review IDs>"]
decisions_without_confirmed_owner: [DEC-003]
interventions:
  - { id: INT-1, horizon: immediate|sprint|quarter, action: "...", owner: "...", owner_confirmation: proposed, targets_dimension: decision_latency }
approved_changes: [CHG-1, CHG-2, CHG-4]
rejected_changes: [CHG-3]
```

Rules:
- Quick reviews still record all 12 scorecard dimensions; unassessed dimensions are `no_evidence` with `evidence: ["not assessed in quick mode"]`.
- `trend` is computed against `previous_review`'s scorecard, not against memory or narrative.
- If `metrics_definitions_version` or `charter_version` differs from the previous review, the review must state which comparisons are invalidated.
