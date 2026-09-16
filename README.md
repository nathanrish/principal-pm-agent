# principal-pm-agent

A Principal Program Manager operating system for Claude: global behavioral rules, workflow skills, record schemas, and evals.

## Layout

| Path | Purpose |
|---|---|
| `CLAUDE.md` | Global rules — mandate, Coordinator vs Principal test, evidence and Decision Quality, write safety |
| `skills/init-program/` | Set up a program in the private workspace (approval-gated) |
| `skills/program-review/` | Evidence-based review with forecast, decisions, diagnosis; `--quick` mode; approval-gated state changes |
| `schemas/` | Record formats for workspace state — built for historical comparison |
| `evals/` | Rubric and redacted cases that catch coordinator-style and unsupported output |

## Workspace

Program state lives **outside** this repo, in a private folder or repository referenced by `PM_WORKSPACE`. Layout: `schemas/program-state.md`.

## Status

Phase 0: manual evidence (files/CSV exports), no live connectors.
