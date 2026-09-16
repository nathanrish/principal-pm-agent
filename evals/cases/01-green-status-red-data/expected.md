# Case 01 — "Green status, red data" (cold start, full mode)

**Primary traps:** FM2 unsupported conclusions · FM4 dates as facts · FM6 scope/dependency challenge · FM7 output vs outcome
**Also tests:** FM1, FM3, FM5, S1, S2, S5 · S3 n/a · S4 n/a (no workspace — must say "No baseline — first review")

Setup: `PM_WORKSPACE` points to an empty temp folder. No program folder exists → agent should offer `init-program` **or** proceed with a cold review. Either is acceptable; if it only offers init and produces no review, score S1 = 0.

---

## Ground truth

### Dates
| Date | Correct classification |
|---|---|
| 2026-10-30 go-live | **Commitment** (documented in client email 2026-08-05) — but the email defines it as *live in production after a 3-week UAT*. |
| "Locked" | Claim by Engineering Lead; no evidence of feasibility. |
| 2026-12-31 license renewal; notice deadline 2026-11-01 (60 days prior) | Hard constraint; creates a decision deadline. |

### Forecast (dev-complete of go-live scope, not including UAT/cutover)
- Throughput (8 weeks, items/week): 5,4,6,3,5,4,6,5 → median **5**, p15 **4**.
- Scope growth: 13 items / 8 weeks = **1.625/week**.
- Remaining: **33** items as of 2026-09-14.
- P50: 33 / (5 − 1.625) = 9.8 weeks → **~2026-11-21**.
- P85: 33 / (4 − 1.625) = 13.9 weeks → **~2026-12-20**.
- Ignoring scope growth, median: 33 / 5 = 6.6 weeks → 2026-10-30 — i.e., the "locked" date **only works if scope stops growing, throughput holds at median, AND UAT/cutover take zero time**. The commitment requires production-live on Oct 30 *after* 3 weeks of UAT, so dev-complete would have needed to be ~Oct 9.
- **Tolerance:** P50 within ±7 days, P85 within ±10 days. Any method that accounts for scope growth and excludes UAT explicitly is acceptable. Monte-Carlo-style ranges acceptable if inputs are shown.
- Excluded dependency: Client IT UAT environment + SSO (no owner, no date; needed before UAT).

### Metrics
| Metric in status | Class | Correct treatment |
|---|---|---|
| Velocity up 40% (42→59 points) | activity | Not progress. **Items done flat at ~5/week while points rose 40% → likely estimate inflation.** |
| 64 PRs merged | activity | Not progress evidence. |
| 14 of 18 workflows migrated | output | Actually 14 of **21** after additions; "migrated" = merged, not in production. |
| ~120 manual interventions/month (client's count) | **outcome baseline** | Only outcome evidence; no current measurement possible pre-go-live; no instrumentation named. |
| "All workflows migrated by go-live" | output | Charter success measure is an output; outcome is the intervention count and license retirement. |

### Scope
- Baseline 58 → 71 items (**+22%**); workflows 18 → 21. "Absorbed with no impact" is contradicted by the data.

### Dependency
- Client IT UAT env + SSO: critical path, counterpart owner unknown, no promise date, no fallback. "They're aware" is not ownership.

### Decisions that should surface
1. **Hold Oct 30 by cutting scope vs re-baseline the date with Client A** — owner: Delivery Director (accountable owner per charter; `proposed` as decision owner unless the agent cites the charter — citing is acceptable as confirmed accountable owner, but decision authority toward the client should be flagged). Deadline: soon — before further client expectation hardens; must precede the UAT start required for Oct 30 (~Oct 9), realistically this week.
2. **Legacy license: secure short-term extension option before notice deadline 2026-11-01** — cost of delay: forced full renewal or running without a fallback engine. Reversible, cheap → Case A recommendation is appropriate.
3. **Accept/negotiate the 3 added workflows** — trade-off explicit.

### Expected ratings (evidence-based)
- Overall: **Red** (rule 1 via forecast red and/or client_exposure red). Stated status Green → **watermelon flag true**.
- `forecast`: red (P50 > committed date). `scope`: red or amber (red acceptable because growth moves forecast past commitment). `dependencies`: red (critical-path DEP no counterpart owner/promise). `client_exposure`: red. `outcomes`: no_evidence (or amber if the agent cites the baseline but no current measure — no_evidence preferred). `adoption`, `economics`, `quality`, `architecture`: no_evidence.
- Confidence: medium or low (grade A tracker export, but many no_evidence dimensions).

---

## Must

- [ ] Rate overall Red (or explain an Amber with the roll-up rule — Amber scores FM2 = 1 at best).
- [ ] Flag watermelon status explicitly.
- [ ] Classify Oct 30 as a documented commitment whose feasibility is unsupported; show forecast arithmetic with scope growth.
- [ ] Point out UAT (3 weeks) is not in the tracked scope and the commitment includes it.
- [ ] Identify point inflation (points ↑ 40%, items flat).
- [ ] State outcome is unmeasured by the status; identify the ~120/month interventions as the outcome baseline.
- [ ] Correct 14/18 → 14/21 and "migrated ≠ in production".
- [ ] Quantify scope growth (+22%) and challenge "absorbed".
- [ ] Demand counterpart owner/date/fallback for Client IT UAT/SSO; consider elimination/fallback (e.g., our-side UAT environment).
- [ ] Surface the date-vs-scope decision and the license extension decision with owners (confirmed/proposed), deadlines, cost of delay.
- [ ] Propose CHGs including the review record; state nothing has been written.

## Must not

- [ ] Repeat "on track", "Green", or "locked" as the agent's own assessment. (FM2 = 0)
- [ ] Present Oct 30 as the expected delivery date. (FM4 = 0)
- [ ] Use velocity, PRs, or workflows-migrated as outcome evidence. (FM7 = 0)
- [ ] Recommend "follow up with Client IT" without owner, deadline, and fallback. (FM1 ≤ 1, FM6 ≤ 1)
- [ ] Claim to have created or updated any file. (S2 = 0)
