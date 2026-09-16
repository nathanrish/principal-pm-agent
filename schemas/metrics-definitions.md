# Schema: Metrics Definitions

A metric cited in any review must resolve to a definition here or in the workspace override (`$PM_WORKSPACE/metrics.md`, same format). Workspace definitions override engine defaults with the same `id`.

Stable definitions are what make reviews comparable over time. **Changing a definition requires a version bump**, and the next review must state which historical comparisons are invalidated.

---

## Definition format

```yaml
id: cycle_time_p85
name: "Cycle time (85th percentile)"
class: output            # activity | output | outcome | impact
version: 1
changed_on: 2026-09-16
change_note: "initial"
definition: "Elapsed days from first entering an in-progress state to entering Done, per work item; 85th percentile over the window."
formula: "p85(done_date − first_in_progress_date)"
unit: days
direction: lower_is_better  # higher_is_better | lower_is_better | target_band
window: "trailing 8 weeks"
source: "work tracker export"
required_fields: [id, type, state_history or (activated_date, closed_date)]
inclusions: "User stories, bugs"
exclusions: "Epics, features, removed items"
org_must_define: ["which states count as in-progress", "which state counts as Done"]
misuse: "Comparing teams; targeting it directly (encourages splitting work artificially)"
owner: null
```

## Metric classes — the rule that matters

| Class | Answers | Examples | Can it prove an outcome? |
|---|---|---|---|
| **activity** | Are people busy? | commits, meetings held, tickets touched, story points completed | Never |
| **output** | Did we produce things? | features shipped, items done, PRs merged, assets processed, users onboarded, model accuracy on internal eval | Never on its own |
| **outcome** | Did user/operator behavior or results change? | task success rate, manual rework hours avoided, time-to-invoice, error rate in production workflow, active retained usage | Yes |
| **impact** | Did the business change? | revenue, margin, cost per unit, client retention, SLA penalties avoided | Yes |

Reviews must not rate the `outcomes` dimension from activity or output metrics. If only those exist, `outcomes` is `no_evidence` and the gap is named.

---

## Engine default definitions

All defaults have `version: 1`, `changed_on: 2026-09-16`. Fields omitted below inherit: `owner: null`, `window: "trailing 8 weeks"` where applicable.

```yaml
- id: throughput_weekly
  name: "Throughput (items/week)"
  class: output
  definition: "Count of work items entering Done per calendar week."
  unit: items/week
  direction: higher_is_better
  source: "work tracker export"
  org_must_define: ["Done state", "item types counted"]
  misuse: "Treating as progress toward an outcome; comparing teams"

- id: cycle_time_p85
  name: "Cycle time (85th percentile)"
  class: output
  definition: "Days from first in-progress state to Done; p85."
  unit: days
  direction: lower_is_better
  source: "work tracker export"
  org_must_define: ["in-progress states", "Done state"]
  misuse: "Targeting directly"

- id: wip_count
  name: "Work in progress"
  class: activity
  definition: "Items currently in an in-progress state at as_of."
  unit: items
  direction: target_band
  source: "work tracker export"

- id: aging_wip
  name: "Aging WIP"
  class: output
  definition: "In-progress items whose age exceeds the team's cycle_time_p85."
  unit: items
  direction: lower_is_better
  source: "work tracker export"

- id: scope_change_pct
  name: "Scope change since baseline"
  class: output
  definition: "(current_item_count − baseline_item_count) / baseline_item_count, for the milestone's scope."
  unit: percent
  direction: target_band
  source: "state.md scope block + work tracker export"
  misuse: "Hiding re-scoping by closing and re-opening items"

- id: story_points_completed
  name: "Story points completed"
  class: activity
  definition: "Sum of estimate points on items entering Done in a sprint."
  unit: points
  direction: target_band
  source: "work tracker export"
  misuse: "Any use outside the team's own sprint planning. Never valid as progress, productivity, or forecast input."

- id: deployment_frequency
  name: "Deployment frequency (DORA)"
  class: output
  definition: "Successful production deployments per week."
  unit: deploys/week
  direction: higher_is_better
  source: "CI/CD export"
  org_must_define: ["what counts as a production deployment"]

- id: change_lead_time
  name: "Change lead time (DORA)"
  class: output
  definition: "Median time from commit to running in production."
  unit: hours
  direction: lower_is_better
  source: "VCS + CI/CD export"

- id: change_fail_rate
  name: "Change fail rate (DORA)"
  class: output
  definition: "Share of production deployments that require immediate remediation (rollback, hotfix, incident)."
  unit: percent
  direction: lower_is_better
  source: "CI/CD + incident export"

- id: failed_deployment_recovery_time
  name: "Failed deployment recovery time (DORA)"
  class: output
  definition: "Median time from a failed deployment to restored service."
  unit: hours
  direction: lower_is_better
  source: "incident export"

- id: deployment_rework_rate
  name: "Deployment rework rate (DORA)"
  class: output
  definition: "Share of deployments that are unplanned, performed to fix a user-facing issue."
  unit: percent
  direction: lower_is_better
  source: "CI/CD + incident export"

- id: escaped_defects
  name: "Escaped defects"
  class: output
  definition: "Defects found in production per period, by severity."
  unit: defects/period
  direction: lower_is_better
  source: "defect tracker export"

- id: decision_latency_days
  name: "Decision latency"
  class: output
  definition: "Days from DEC raised_on to decided_on (or to as_of if open). Report median and max open."
  unit: days
  direction: lower_is_better
  source: "decisions.md"

- id: say_do_ratio
  name: "Say/do ratio"
  class: outcome
  definition: "met / (met + missed) for commitments closed in window. Outcome for the program's credibility with stakeholders."
  unit: ratio
  direction: higher_is_better
  source: "commitments.md"
  window: "trailing 90 days"

- id: forecast_error_p50
  name: "Forecast error (P50)"
  class: output
  definition: "actual_date − p50 for resolved forecasts; report mean absolute error."
  unit: days
  direction: lower_is_better
  source: "forecasts.md"
  window: "all resolved"

- id: forecast_calibration_p85
  name: "Forecast calibration (P85)"
  class: output
  definition: "Share of resolved forecasts with actual ≤ p85."
  unit: percent
  direction: target_band
  source: "forecasts.md"
  window: "all resolved"
```

## Outcome and impact metrics

The engine provides no default outcome or impact metrics — they are program-specific by nature. `init-program` must define at least one outcome metric per program in the workspace `metrics.md`, or record the outcome as unmeasured in `charter.md` (baseline `null`) so every review flags it.
