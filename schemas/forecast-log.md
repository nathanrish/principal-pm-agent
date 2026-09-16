# Schema: Forecast Log (`forecasts.md`)

Every forecast the agent issues is recorded, so that forecast accuracy can be measured later and forecasts can be compared across reviews. A forecast that is never checked against the actual is an opinion.

Format and shared conventions: `program-state.md` §2.

```yaml
id: FC-005
target: MS-003                  # milestone or outcome being forecast
issued_on: 2026-09-16
issued_in: REV-2026-09-16
supersedes: FC-003              # previous forecast for the same target, or null
status: open                    # open | superseded | resolved | abandoned

method: throughput_range        # throughput_range | monte_carlo | reference_class | expert_estimate | none
defensible: true                # false = not enough evidence; p50/p85 must be null
inputs:
  remaining_items: { value: 42, as_of: 2026-09-15, source: "evidence/2026-09-15/workitems.csv", scope_basis: "items not in Done for MS-003" }
  throughput_window: { from: 2026-07-21, to: 2026-09-14, weeks: 8 }
  weekly_throughput: { samples: [5, 7, 4, 6, 8, 5, 6, 7], p15: 4.5, p50: 6, p85: 7.5 }
  scope_growth_per_week: { value: 1.6, basis: "13 items added over 8 weeks" }
  capacity_changes: "<known PTO, hiring, reallocations, or 'none known'>"
  excluded: "<what the forecast ignores, e.g. DEP-004 readiness>"

result:
  p50: 2026-11-27
  p85: 2026-12-18
  confidence: low
  top_assumptions: ["throughput stays within 8-week range", "DEP-004 delivered by 2026-10-01"]
  would_move_if: ["scope growth continues at 1.6/wk", "DEP-004 slips"]

compared_to:
  committed_date: 2026-10-30     # from COM record, or null
  commitment_ref: COM-002
  previous_forecast: { id: FC-003, p50: 2026-11-13, p85: 2026-12-04 }
  movement_days_p50: 14

resolution:                       # filled when target completes or is dropped
  actual_date: null
  error_days_p50: null            # actual − p50 (positive = late)
  within_p85: null
history: []
```

## Methods — minimum rules

- **throughput_range:** weeks-to-complete = `remaining_items / weekly_throughput`. P50 uses median throughput; P85 uses the 15th-percentile throughput. If scope is growing, use `remaining_items / (throughput − scope_growth)`; if that denominator ≤ 0, the forecast is **"no convergence at current rates"**, not a date.
- Require ≥6 weeks of throughput samples for `defensible: true`. Fewer → `confidence: low` at best, and say so.
- Story points are not valid inputs. Item counts are (with the caveat that item size varies).
- **expert_estimate:** allowed only when labeled, attributed, and `confidence: low` unless a reference class exists.
- **none / defensible: false:** record it anyway. "No defensible forecast" is a finding the next review must compare against.
- External dependencies on the critical path that are not reflected in throughput must be listed in `excluded` and in `top_assumptions`.

## Derived metrics

- **Forecast error** per resolved forecast and rolling mean absolute error per program.
- **Calibration:** share of resolved forecasts whose actual landed ≤ P85 (target ≈ 85%).
- **Drift:** `movement_days_p50` across consecutive forecasts for the same target. Consistent positive drift = systematic optimism or unmanaged scope.
