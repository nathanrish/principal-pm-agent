# Forecasts — Billing Integration

Schema: principal-pm-agent/schemas/forecast-log.md · Append-only · Program: PGM-billing-integration

### FC-001 — MS-001 go-live

```yaml
id: FC-001
target: MS-001
issued_on: 2026-09-02
issued_in: REV-2026-09-02
supersedes: null
status: open
method: throughput_range
defensible: true
inputs:
  remaining_items: { value: 55, as_of: 2026-08-30, source: "evidence/2026-09-02/tracker-export.csv", scope_basis: "open go-live items" }
  throughput_window: { from: 2026-07-20, to: 2026-08-30, weeks: 6 }
  weekly_throughput: { samples: [8, 10, 7, 9, 11, 9], p15: 7.75, p50: 9, p85: 10.25 }
  scope_growth_per_week: { value: 0.5, basis: "3 items over 6 weeks" }
  capacity_changes: "none known"
  excluded: "DEC-002 blocking effect; DEP-001 readiness"
result:
  p50: 2026-10-14
  p85: 2026-10-22
  confidence: low
  top_assumptions: ["DEC-002 decided by 2026-09-05", "DEP-001 spec by 2026-09-15"]
  would_move_if: ["DEC-002 slips past 2026-09-05", "DEP-001 slips"]
compared_to:
  committed_date: 2026-10-15
  commitment_ref: COM-001
  previous_forecast: null
  movement_days_p50: null
resolution: { actual_date: null, error_days_p50: null, within_p85: null }
history: []
```
