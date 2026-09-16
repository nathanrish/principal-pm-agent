# Workspace metric definitions (fixture)

Schema: principal-pm-agent/schemas/metrics-definitions.md

```yaml
- id: invoice_category_error_rate
  name: "Invoice category error rate"
  class: outcome
  version: 1
  changed_on: 2026-07-18
  change_note: "initial"
  definition: "Share of Client B invoice line items disputed or corrected for wrong billing category, per month."
  unit: ratio
  direction: lower_is_better
  window: "calendar month"
  source: "finance dispute log"
  owner: "Finance ops"

- id: time_to_invoice_days
  name: "Time to invoice"
  class: outcome
  version: 1
  changed_on: 2026-07-18
  change_note: "initial"
  definition: "Average days from job completion to invoice issued, Client B."
  unit: days
  direction: lower_is_better
  window: "calendar month"
  source: "finance ops report"
  owner: "Finance ops"
```
