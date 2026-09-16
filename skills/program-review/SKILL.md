---
name: program-review
description: Evidence-based program health review with forecast, decisions, diagnosis, and interventions, compared against prior reviews. Use when the user shares program status, evidence, or exports and asks to review, assess, or health-check a program. Supports --quick. Never writes program state without approval.
---

# program-review

Turn messy status, evidence, and prior records into a **diagnosis and a set of decisions** — not a status summary. Every review is recorded in a format the next review can compare against.

**Invocation:** `/program-review <program-id> [--quick]` or natural language ("Here's the status of X. Review it.").

**Governing rules:** `CLAUDE.md` (especially §1 Coordinator vs Principal, §3 Write Safety, §5 Evidence & Decision Quality, §6 Diagnosis). **Rating criteria:** `scorecard.md`. **Record formats:** `schemas/`.

---

## Non-negotiables

1. **No silent writes.** This skill writes nothing until the principal approves specific `CHG-n` items. The review record itself is a proposed change.
2. **No invented evidence.** Every rating, date, owner, and metric traces to a source or is labeled `claim` / `assumption` / `inference` / `proposed`.
3. **Stated status is a claim.** Rate from evidence; compare to the stated status; flag the gap.
4. **Diagnosis, not reporting.** Every amber/red item has a cause or "cause unknown — to find it: X".
5. **Decisions have owners and deadlines** — confirmed or visibly proposed.
6. **Output metrics never rate outcomes.**

---

## Step 1 — Resolve inputs

1. Resolve workspace (`PM_WORKSPACE`, else ask once) and `program-id`.
   - If the program folder does not exist: say so, and offer (a) `init-program` first, or (b) a **cold review** from supplied material only. In a cold review, "Changes Since Last Review" is "No baseline — first review", nothing is compared, and the proposed changes include creating the minimum records.
2. Load, if present: `org.md`, `metrics.md`, `charter.md`, `state.md`, `decisions.md`, `raid.md`, `commitments.md`, `forecasts.md`, the **most recent** review in `reviews/` (and the last 3 for recurrence), and evidence referenced or newly supplied.
3. New files supplied in conversation: treat as a new evidence drop dated today; propose saving them under `evidence/YYYY-MM-DD/` with a `MANIFEST.md` entry (as a CHG).
4. Mode: `--quick` if flagged or if the user asks for something short; otherwise full.

Do **not** ask clarifying questions for gaps that only reduce precision. Ask (max 3) only if the program identity, the committed milestone, or the audience is ambiguous enough to change the direction of the review.

## Step 2 — Evidence inventory

For each evidence item: path/source, what it is, `as_of`, grade (A/B/C per `schemas/program-state.md` §2.4).

Then list:
- **Gaps** — evidence needed for a rating that is absent. Name the dimension it blocks.
- **Conflicts** — sources that disagree. Do not resolve silently; state which you weight and why (source precedence, CLAUDE.md §2.3).
- **Stale** — anything older than 21 days used for a material call.
- **Claims** — assertions without support (e.g., "UAT confirmed", "on track", "go-live locked"). Attribute each.

## Step 3 — Extract and classify

- **Dates:** classify each as `actual`, `commitment` (only if a COM record or documented source exists), `target` (a claim), or `forecast` (only if computed in Step 4). **A target date is never presented as a commitment or a fact.**
- **Metrics:** map each to a metric ID and class (`schemas/metrics-definitions.md`). If a metric has no definition, label it `undefined metric` and do not use it for a rating. Separate activity/output from outcome/impact explicitly.
- **Owners:** `confirmed` only if a named owner accepted it in evidence; otherwise `proposed` or `unknown`.
- **Scope:** baseline vs current item counts and what changed; whether any trade-off was recorded.

## Step 4 — Forecast

Apply `schemas/forecast-log.md` method rules.

- If throughput evidence exists (≥6 weeks of item-level completion data): compute P50/P85 using `throughput_range`, adjusting for scope growth. If scope growth ≥ throughput, the result is **"no convergence at current rates"**.
- If not: `defensible: false`. State "No defensible forecast" and the **minimum evidence needed** (e.g., "work-item export with closed dates for 8 weeks, and remaining item count for MS-003").
- List critical-path dependencies not reflected in throughput as explicit exclusions/assumptions.
- Compare to: committed date (COM), stated/target date (claim), and previous FC for the same target (movement in days).
- Show the arithmetic in one or two lines so it can be checked.

## Step 5 — Compare to history

Skip in cold reviews. Otherwise, against the previous review record's frontmatter:
- Per-dimension rating change → trend.
- Overall rating/confidence change.
- Forecast movement (P50/P85 days) and whether the committed date moved into or out of range.
- Commitments due since last review: met / missed / renegotiated (with say/do ratio).
- Decisions: newly opened, decided, past deadline, **open in ≥2 consecutive reviews**.
- RAID: new, closed, triggered, aging (no history change across reviews), surprise issues.
- **Recurring issues:** anything named as a blocker/issue in ≥2 consecutive reviews → mandatory structural diagnosis in Section 8.
- Definition or charter version changes → state which comparisons are invalid.
- Interventions from last review: done / not done / no evidence.

## Step 6 — Rate

Apply `scorecard.md`: 12 dimensions, evidence floor, roll-up rule, confidence rule, watermelon check. In quick mode apply `scorecard.md` §6.

## Step 7 — Diagnose

- **Bottleneck:** where work or decisions are actually waiting (not where people are busiest). Evidence: aging WIP, blocked items, decision latency, dependency wait.
- **Dependency:** which external dependency most threatens the critical path, and whether it can be **eliminated** (stub, contract-first, decoupling, scope change) rather than tracked.
- **Structural issue:** the mechanism producing recurring failure — ownership boundary, missing interface contract, decision rights, WIP policy, incentive, team topology, feedback-loop delay. If none identified: "No structural cause identified from available evidence — to find it: X."
- **Challenge:** explicitly test (a) whether scope growth is justified by the outcome, (b) whether the committed date is still worth defending vs cutting scope, (c) whether the core bet still holds. State your challenge even if the answer is "holds".

## Step 8 — Decisions and interventions

- For each decision needed: apply CLAUDE.md §5.3 (Case A/B/C) with owner (confirmed/proposed), deadline with basis, cost of delay, reversibility.
- Interventions in three horizons — **Immediate (≤48h)**, **This sprint**, **This quarter**. Each: action · owner (confirmed/proposed) · dimension it moves · expected effect · how we'll know. Prefer interventions that remove work, dependencies, or decisions over ones that add meetings or reports. **"Follow up with X" or "schedule a sync" is not an intervention unless it names the decision it forces and the deadline.**

## Step 9 — Leadership questions

Anticipate the hardest questions a VP/CXO (or client executive, if client exposure is amber/red) will ask. For each: the answer from evidence, or "Cannot answer yet — need X by <date>".

## Step 10 — Self-check

Before output, check the draft against `evals/rubric.md` critical criteria. If any fail, fix the draft. Do not print the checklist.

## Step 11 — Propose state changes and stop

List every proposed workspace change as `CHG-n` (format below). Ask for approval. **Stop.** Do not write anything.

On approval: apply only the approved `CHG`s, record `approved_changes` / `rejected_changes` in the review record frontmatter, and confirm exactly which files changed. If the review record itself (CHG for `reviews/…`) is rejected, no other CHG that references `issued_in: REV-…` may be applied without re-issuing them against a conversation source — say so.

---

## Output format — full mode

Use these headings exactly, in this order. Lead each section with the conclusion.

```
# Program Review — <Program name> — <YYYY-MM-DD>
Mode: full · Previous review: <REV-id | none> · Evidence grade: <A/B/C> · Engine: <sha|unknown>

## 1. Evidence Quality
- Evidence used (table: source · as_of · grade)
- Gaps (and which dimension each blocks)
- Conflicts (and how weighted)
- Unsupported claims (attributed)
- Net effect on confidence (one line)

## 2. Executive Assessment
- **Overall: <Red/Amber/Green> · Trend: <…> · Confidence: <…>** (roll-up rule applied)
- Stated status vs evidence-based status; watermelon flag if applicable
- Bottom line in ≤3 sentences: what's true, what's at risk, what decision is needed
- Scorecard table: dimension · rating · trend · one-line evidence

## 3. Changes Since Last Review
- Rating changes · forecast movement · commitments met/missed (say/do) · decisions opened/closed/overdue · RAID changes · recurring issues · last review's interventions: done/not done
- Or: "No baseline — first review."

## 4. Outcome Progress
- Table per outcome: metric (class) · baseline · target · current · gap · as_of
- Output/activity metrics reported separately, explicitly labeled as not outcome evidence
- If unmeasured: say so, and the minimum instrumentation needed

## 5. Forecast
- Per critical milestone: committed date (COM ref or "none on record") · stated/target date (claim) · P50 · P85 · confidence · method + arithmetic · movement vs last forecast
- Critical path (sequence and the constraint on it)
- Assumptions and exclusions
- If not defensible: "No defensible forecast" + minimum evidence needed

## 6. Risks
- Top 3 (max 5): ID · risk · likelihood · impact (concrete) · time-to-impact · trigger · mitigation · owner (confirmed/proposed)
- New vs existing; incomplete risk records flagged

## 7. Decisions
- Per decision: ID · decision needed · Case A/B/C · recommendation (or minimum evidence) · owner (confirmed/proposed) · deadline (basis) · cost of delay · reversibility
- Decisions without confirmed owners called out explicitly

## 8. Execution Diagnosis
- Bottleneck · critical dependency (and elimination option) · structural issue (mandatory for recurring issues)
- Challenge: scope · date vs scope trade-off · core bet

## 9. Recommended Interventions
- Immediate (≤48h) · This sprint · This quarter
- Each: action · owner (confirmed/proposed) · dimension moved · expected effect · signal it worked

## 10. Leadership Questions
- 3–6 questions with evidence-based answers or "Cannot answer yet — need X"

---
## Proposed State Changes (approval required — nothing has been written)
<CHG list>
Reply with "approve all", "approve CHG-1, CHG-3", or "reject".
```

## Output format — `--quick` mode

Target: readable in 2 minutes. Same analysis rules; less exposition. Quick mode **never** skips evidence quality, forecast credibility, decisions, or the watermelon check.

```
# Quick Review — <Program name> — <YYYY-MM-DD>
Mode: quick · Previous review: <REV-id | none> · Evidence grade: <A/B/C>

## 1. Evidence Quality — ≤4 bullets (biggest gap, biggest conflict, key unsupported claim, confidence effect)
## 2. Executive Assessment — overall/trend/confidence, stated vs evidence, bottom line (≤3 sentences). Compact scorecard: only non-green and no_evidence dimensions.
## 3. Changes Since Last Review — ≤4 bullets
## 4. Outcome Progress — one line: measured or not; if only output metrics, say so
## 5. Forecast — one line per critical milestone: committed · P50/P85 or "no defensible forecast" · confidence
## 6. Risks — top 3, one line each (risk · impact · time-to-impact · owner)
## 7. Decisions — every blocking decision, one line each (decision · case · owner confirmed/proposed · deadline · cost of delay)
## 8. Execution Diagnosis — bottleneck and structural issue, ≤3 bullets
## 9. Recommended Interventions — Immediate only (≤3), plus one "this quarter" if structural
## 10. Leadership Questions — top 3, one-line answers

## Proposed State Changes (approval required — nothing has been written)
```

Quick reviews are saved as `reviews/YYYY-MM-DD-quick.md` with `review_id: REV-YYYY-MM-DD-q` and `mode: quick`.

## Proposed change format

```
CHG-1 · create · reviews/2026-09-16.md
  Review record REV-2026-09-16 (frontmatter per schemas/program-state.md §5 + this review's body)

CHG-2 · append · forecasts.md
  FC-006 → target MS-003, p50 2026-11-27, p85 2026-12-18, confidence low, supersedes FC-005

CHG-3 · update · decisions.md · DEC-003
  owner.confirmation: unknown → proposed (name: "Integration lead")
  history += { on: 2026-09-16, change: "owner proposed", source: REV-2026-09-16 }

CHG-4 · append · raid.md
  RISK-009 — <title> (full YAML shown)

CHG-5 · replace · state.md
  overall: amber → red; last_review → REV-2026-09-16; open_records updated (diff shown)
```

Rules:
- Show the full YAML for any **new** record. Show a field-level diff for **updates**.
- One CHG per file operation. Order: evidence → records → review record → `state.md` last.
- Never propose deleting a record. Closing/superseding is an update with history.
- Proposed owners in CHGs stay `proposed` — approval of the CHG approves recording the proposal, not confirming the owner.
