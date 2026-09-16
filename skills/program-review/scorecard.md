# Program Health Scorecard

Canonical rating criteria for `program-review` (and, later, `exec-update`). Dimension IDs are fixed — they are the keys stored in every review record (`schemas/program-state.md` §5). Renaming or adding a dimension is a breaking change to historical comparison and requires an engine version note.

---

## 1. Rating rules (apply to every dimension)

- **Rate from evidence, not narrative.** A status report saying "Green" is a `claim`, not evidence.
- **`no_evidence` is a rating, not a gap to fill with a guess.** Never default to green.
- **Evidence floor:**
  - `green` requires evidence grade A or B for that dimension.
  - `red` may be assigned on grade C evidence **if** the claim is specific and material (e.g., "client has not provisioned UAT"), labeled as such, with a proposed verification.
  - Grade C alone can never produce `green`.
- **Each rating needs one line of evidence** (record IDs, file refs) and, for amber/red, one line of cause.
- **Trend** is computed only against the previous review record's rating for the same dimension: better → `improving`, same → `stable`, worse → `worsening`; no prior → `new`; either side `no_evidence` → `unknown`.

## 2. Dimensions

| ID | Question | Green | Amber | Red | Required evidence |
|---|---|---|---|---|---|
| `outcomes` | Are we moving the outcome metric, and is it still the right problem? | Outcome metric measured vs baseline, on trajectory to target | Measured but off trajectory, or baseline stale (>1 review cycle) | Moving the wrong way, or evidence the problem/core bet is invalid | Outcome/impact-class metric with baseline. **Output/activity metrics cannot rate this dimension.** |
| `forecast` | Will committed milestones land? | Defensible forecast (FC) with P85 ≤ committed date | P50 ≤ committed date < P85, or forecast confidence low | P50 > committed date, or forecast does not converge at current rates | FC record with inputs; COM record for committed dates. **No defensible forecast → `no_evidence`** (roll-up rule 2 then caps overall at amber, and the watermelon check applies) |
| `scope` | Is scope controlled and coherent? | Scope change within agreed band with explicit trade-offs recorded | Growth without recorded trade-off but not yet threatening forecast | Growth that moves forecast past commitment, or scope undefined/unbounded | Baseline and current item counts; change log or decisions |
| `dependencies` | Are critical external dependencies owned, promised, and backed by a fallback? | All critical-path DEPs have confirmed counterpart owner, promise ≤ needed_by, and a fallback | Any critical-path DEP missing one of: fallback, interface contract; or promise within 1 week of need | Any critical-path DEP with no confirmed counterpart owner, no promise, promise > need, or status `broken` | DEP records |
| `risks` | Are material risks quantified and being actively mitigated? | Top risks have likelihood, impact, time-to-impact, trigger, owner; mitigations progressing | Material risks missing triggers/owners, or mitigation stalled one review | A high-impact risk inside its time-to-impact with no mitigation, or an unanticipated issue (surprise ISS) on the critical path | RISK/ISS records, history |
| `decision_latency` | Are decisions being made fast enough? | No open decision past deadline; all blocking decisions have confirmed owners | A blocking decision with proposed/unknown owner, or within 1 week of deadline | Any critical-path decision past deadline, or open across ≥2 consecutive reviews | DEC records |
| `quality` | Are we trading quality for dates? | Defect/change-fail trends stable or improving; test/release gates intact | Rising defects or skipped gates without recorded decision | Escaped severe defects, rising change fail rate, or quality gates removed to hit a date | Defect/CI-CD exports, release notes |
| `architecture` | Are short-term choices creating long-term constraints? | Known tech-debt decisions recorded with payback plan | Unrecorded shortcuts, or debt acknowledged without plan | Choices that block a stated outcome, a client commitment, or scale target | ADRs/decisions, engineering lead input |
| `ownership` | Are accountability and capacity clear? | Accountable owner per milestone/outcome confirmed; no single-person critical path without backup | Any milestone with proposed owner, or key-person risk without mitigation | No accountable owner for a critical milestone, or critical path depends on an unavailable person | Charter, state, org.md |
| `adoption` | Are intended users changing behavior? *(rate `no_evidence` pre-launch unless pilot data exists)* | Adoption/task-success metric at or above target | Below target with understood cause and plan | Declining or stalled with no plan, or users working around the product | Outcome-class usage metrics, not "users onboarded" |
| `economics` | Is the cost/benefit still sound? | Cost within plan and unit economics at/above case | Cost overrun or benefit unproven but bounded | Unit economics worse than manual baseline, or unbounded cost | Budget/cost exports, unit-cost analysis |
| `client_exposure` | Are external commitments safe? | All open external COMs at forecast ≤ due with defined acceptance criteria | Any external COM `at_risk`, or acceptance criteria undefined | Any external COM whose forecast P50 > due and no renegotiation in progress, or a missed COM | COM records + FC records |

## 3. Overall rating roll-up

Apply in order; the first rule that matches sets the ceiling.

1. **Red** if any of `forecast`, `client_exposure`, or `outcomes` is `red`, or if ≥3 dimensions are `red`.
2. **Amber** (at best) if any dimension is `red`, or if `forecast` or `outcomes` is `no_evidence`.
3. **Green** only if no dimension is `red`, at most 2 are `amber`, and `forecast` is `green`.
4. Otherwise **Amber**.

Record the rule that set the rating in the review output (e.g., "Overall Red — rule 1: forecast red").

## 4. Confidence

| Confidence | Condition |
|---|---|
| `high` | Overall evidence grade A; ≤2 dimensions `no_evidence`; no unresolved source conflicts |
| `medium` | Grade B, or 3–5 dimensions `no_evidence`, or ≥1 resolved conflict |
| `low` | Grade C, or ≥6 dimensions `no_evidence`, or any unresolved conflict affecting a red/green call |

## 5. Watermelon check (mandatory)

Set `watermelon_flag: true` and call it out in the Executive Assessment when **the stated status is better than the evidence-based overall rating**, or any of these hold while stated status is green:

- A committed date has no defensible forecast, or P50 > committed date.
- Scope grew >10% since baseline without recorded trade-off.
- A critical-path dependency has no confirmed counterpart owner.
- The same blocker/issue appears in ≥2 consecutive reviews.
- Progress is evidenced only by activity/output metrics (points, tickets, PRs, items processed).
- A decision gating a milestone is past its deadline.

## 6. Quick mode

Quick mode must still:
- Record all 12 dimension IDs (unassessed ones as `no_evidence`, evidence `"not assessed in quick mode"`).
- Fully assess `forecast`, `dependencies`, `decision_latency`, `client_exposure`, and `outcomes`.
- Run the watermelon check.
- Apply roll-up and confidence rules — which means quick-mode confidence will often be lower. That is correct.
