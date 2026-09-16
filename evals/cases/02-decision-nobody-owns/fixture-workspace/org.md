org_name: "Redacted Co"
as_of: 2026-08-01
principal: { name: "Principal", role: "Technical Program Manager" }
teams:
  - { name: "Platform", type: platform, lead: "Platform lead", size: 4 }
  - { name: "Integration", type: stream_aligned, lead: "Integration lead", size: 5 }
decision_rights:
  - { area: "scope changes", owner: "Principal" }
  - { area: "external date commitments", owner: "Delivery Director" }
  - { area: "architecture within a team's services", owner: "that team's lead" }
  # no entry for cross-team data models / shared contracts
cadence: { review: biweekly, exec_update: weekly, sprint_length_days: 14 }
systems_of_record: { work_tracking: "work tracker", ci_cd: "CI/CD", bi: "BI tool", docs: "wiki" }
workflow_states: { in_progress: ["Active", "In Review"], done: ["Done"] }
