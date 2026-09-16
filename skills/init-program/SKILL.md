---
name: init-program
description: Set up a new program in the private PM workspace — charter, current state, empty append-only logs, outcome metric definitions, and a first evidence drop — from messy input. Use when the user wants to start tracking a program, onboard a program into the workspace, or program-review finds no program folder. Proposes all files for approval before writing.
---

# init-program

Create the minimum state a program needs so that `program-review` can compare over time. Minimum, not maximum: an empty but honest record beats a filled-in guess.

**Invocation:** `/init-program [program-name]` with any supplied material (charter drafts, status notes, exports, meeting notes).

**Governing rules:** `CLAUDE.md` §2 (workspace), §3 (write safety), §5 (evidence). **Formats:** `schemas/`.

---

## Step 1 — Resolve workspace

1. Resolve `PM_WORKSPACE`; if unset, ask for the path once.
2. **Refuse** if the path is inside the engine repository (including eval fixtures — they are read-only). Explain why: client and program data must not enter the engine repo.
3. If the workspace does not exist, the proposal includes creating it with `org.md` (skeleton) and recommends `git init` as a private repository. Do not run `git init` without approval.
4. If `programs/<program-id>/` already exists: stop and offer `program-review` instead. Never overwrite.

## Step 2 — Extract from supplied material

Read everything supplied. Extract, with source references, into the charter and records:

| Need | If present in evidence | If absent |
|---|---|---|
| Program name, `program-id` slug | Use | Ask (one of max 3 questions) |
| Problem, why now, core bet | Extract, label as `claim` if from narrative | Leave `null`; list as gap |
| Accountable owner | `confirmed` only if evidence shows acceptance | `proposed` or `unknown` |
| Outcome(s) + metric | Map to metric ID; create workspace metric definition if new | Create `OUT-001` with `metric_id: null`, `baseline: null` — flagged as unmeasured |
| Milestones | `MS-` records; `committed_date` **only** if a documented commitment exists | Target dates become `target` notes, not `committed_date` |
| Commitments | `COM-` records with `source_strength` | None created from hearsay; list candidates for principal confirmation |
| Decisions, risks, issues, dependencies | `DEC-` / `RISK-` / `ISS-` / `DEP-` records with sources | None — empty files with headers |
| Scope baseline | Item count + `as_of` + source | `null` with gap noted |
| Throughput history | Note evidence path for first forecast | Note as minimum evidence needed for a defensible forecast |

**Ask at most 3 questions**, only for things that would otherwise produce a wrong record (typically: program-id, accountable owner, which dates are real commitments). Everything else proceeds as `null`/`proposed` with gaps listed.

## Step 3 — Classify what you extracted

Before proposing, explicitly separate:
- **Facts with sources** → go into records.
- **Claims** (e.g., "go-live is Oct 30", "client team owns UAT") → recorded as claims with attribution; do not become commitments or confirmed owners.
- **Output metrics offered as success measures** (e.g., "features delivered", "users onboarded", "assets processed") → recorded as output-class metrics; the charter outcome stays unmeasured until an outcome metric is defined. Say this to the principal and propose a candidate outcome metric (labeled `proposed`).

## Step 4 — Challenge the frame (briefly)

One short block, before the proposal:
- Is the stated outcome an outcome, or an output?
- Is there a committed date without evidence it is achievable?
- Is there a critical dependency with no counterpart owner?
- What is the riskiest assumption, and is anyone testing it?

Keep it to ≤5 bullets. This is not a review; it seeds the first one.

## Step 5 — Propose files and stop

Present every file as a `CHG-n` with its full proposed content (files are new, so show them whole):

```
CHG-1 · create · $PM_WORKSPACE/org.md                (only if workspace is new)
CHG-2 · create · $PM_WORKSPACE/metrics.md            (only if new outcome metrics are defined)
CHG-3 · create · programs/<id>/charter.md
CHG-4 · create · programs/<id>/state.md
CHG-5 · create · programs/<id>/decisions.md
CHG-6 · create · programs/<id>/raid.md
CHG-7 · create · programs/<id>/commitments.md
CHG-8 · create · programs/<id>/forecasts.md          (header only; no forecast until program-review computes one)
CHG-9 · create · programs/<id>/evidence/<date>/MANIFEST.md (+ copies of supplied files)
CHG-10 · create · programs/<id>/reviews/.keep
```

End with:
- **Gaps that will show as `no_evidence` in the first review** (so nobody is surprised).
- **Minimum evidence for a defensible first review** — typically: work-item export with state history (≥6–8 weeks), list of documented commitments, open decisions with owners, outcome metric baseline.
- "Reply `approve all`, `approve CHG-…`, or `reject`. Nothing has been written."

**Stop.** Write nothing until approval. On approval, create only the approved files and confirm the list. Then suggest running `program-review <id>` once the minimum evidence is in place.

## File skeletons

Empty append-only files start with a header only:

```markdown
# Decisions — <Program name>

Schema: principal-pm-agent/schemas/decision.md · Append-only · Program: PGM-<slug>
```

(Same pattern for `raid.md`, `commitments.md`, `forecasts.md`.)

`org.md` skeleton:

```yaml
org_name: "<name>"
as_of: YYYY-MM-DD
principal: { name: "<name>", role: "<role>" }
teams:
  - { name: "...", type: stream_aligned|platform|enabling|complicated_subsystem, lead: "...", size: null }
decision_rights:
  - { area: "scope changes", owner: "..." }
  - { area: "external date commitments", owner: "..." }
cadence: { review: weekly, exec_update: weekly, sprint_length_days: 14 }
systems_of_record: { work_tracking: "...", ci_cd: "...", bi: "...", docs: "..." }
workflow_states: { in_progress: ["..."], done: ["..."] }   # required for flow metrics
```

`MANIFEST.md`:

```markdown
# Evidence — YYYY-MM-DD

| File | What it is | Source system / author | Exported / written | Period covered | Grade |
|---|---|---|---|---|---|
```
