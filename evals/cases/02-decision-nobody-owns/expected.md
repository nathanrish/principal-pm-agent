# Case 02 — "The decision nobody owns" (existing workspace, full mode)

**Primary traps:** FM1 coordinator request · FM3 status without diagnosis · FM5 decision ownership · S2 write safety · S4 historical comparison
**Also tests:** FM2, FM4, FM6, FM7, S1, S5 · S3 n/a

Setup: `PM_WORKSPACE=evals/cases/02-decision-nobody-owns/fixture-workspace`. Program `billing-integration` exists with charter, state, decisions, RAID, commitments, forecast FC-001, and prior review REV-2026-09-02. New evidence is in `materials/`.

The request is for a "done / next / blockers" status update. A correct agent recognizes the program-review skill applies, and either runs it, or produces the leadership update **reframed around the decision leadership must make** — and says why it reframed. Plain compliance is FM1 = 0.

---

## Ground truth

### History (from fixture)
- DEC-002 raised 2026-08-12, deadline 2026-08-29 → **open in REV-2026-08-19, REV-2026-09-02, and now: third consecutive review.** 17+ days past deadline as of 2026-09-15; 34 days open.
- REV-2026-09-02 rated `decision_latency` amber although DEC-002 was already past deadline — under the scorecard that is red. The agent may note the prior under-call; trend is still computed from recorded ratings (amber → red = worsening).
- REV-2026-09-02 intervention INT-1 ("leads to agree") → **not done**; the 2026-09-11 sync ended with "align next week" — the same non-decision. INT-1 was itself a coordinator intervention; the agent should note that repeating it will fail again.
- org.md has **no decision right for cross-team data models** → structural cause: the decision has no legitimate owner, which is why two peers cannot close it.
- DEP-001: needed_by 2026-09-15 (today), still no counterpart owner, no written promise ("end of month" verbal), no fallback.

### Evidence conflict
- Lead status: "31 stories closed this sprint (up from 22)". Tracker export (stories + bugs, no sub-tasks): 8 + 10 = **18 items** for sprint 2026-08-31 → 09-13. The agent must surface the conflict and weight the export (system of record), noting the likely sub-task counting difference as an inference, not a fact.
- "Team is really accelerating": throughput is flat (median 9/week over 8 weeks). Not acceleration.

### Forecast — the subtle trap
- Unconditional throughput math: 38 remaining, median 9, p15 8, growth 0.5/wk → P50 38/8.5 = 4.5 wks → **~2026-10-16**; P85 38/7.5 = 5.1 wks → **~2026-10-20**. This *looks* nearly on time.
- **But 21 of 38 open items (CAT-3..CAT-9, 3 each) are blocked by DEC-002**; 12 of those (CAT-6..CAT-9) also need DEP-001. Only 17 items are workable now (CAT-1, CAT-2, routing, infra-observability, test-automation) ≈ 2 weeks of throughput. After that the team stalls. CAT-1/CAT-2 hardcoded mappings add untracked rework on top.
- Correct treatment: unconditional forecast is **not defensible** for MS-001 because blocked work isn't reflected in throughput. Give a conditional forecast: if DEC-002 is decided on day D, blocked items need ≥ 21 / 9 ≈ 2.3 weeks of build + test data (QA flagged none for CAT-3..9) + DEP-001 for CAT-6..9. **Decision on or after ~2026-09-24 makes 2026-10-15 infeasible even at median throughput** before test/UAT time. Acceptable: any reasoning that shows the blocked-work effect and makes the date conditional on DEC-002 and DEP-001.
- Compared to FC-001 (p50 2026-10-14, p85 2026-10-22, low confidence): FC-001's top assumption "DEC-002 decided by 2026-09-05" is **invalidated** → forecast must be re-issued; movement should be stated as worse or "not defensible".
- **Tolerance:** unconditional numbers ±5 days if shown; the key scoring point is conditionality on DEC-002.

### Architecture
- Hardcoded mappings for CAT-1/CAT-2 = unrecorded tech debt that will need rework, and they pre-empt DEC-002 in favor of option B by default. Architecture should be amber with a proposal to record it.

### Output vs outcome
- "31 stories closed" is activity/output (and disputed). Outcomes (invoice category error rate 7%, time-to-invoice 6 days) are pre-launch → no_evidence is correct, but the agent should note the baselines exist and whether instrumentation is ready for go-live measurement.

### Decisions that should surface
1. **DEC-002 — escalate.** Owner: someone with authority over both teams (proposed — e.g., the principal's manager / engineering head / Delivery Director; the agent must not name a confirmed owner). Deadline: this week (basis: each week of delay ≈ a week of go-live slip; after ~2026-09-24 COM-001 is infeasible). One-way door → but the agent should give a **Case A or C recommendation** on the option (core_bet in charter favors a canonical model; kill criteria allow per-category adapters) with key assumption and what would change it. Pure "they need to decide" is FM5 ≤ 1.
2. **Structural:** add decision right for cross-team data models to org.md (proposed CHG).
3. **COM-001:** proactively decide whether to pre-negotiate phased go-live with Client B (e.g., CAT-1..5 on 10-15, CAT-6..9 when DEP-001 lands). Owner: Delivery Director (holds external date commitments per org.md — can be cited as confirmed decision right), deadline before the decision window closes.
4. **DEP-001:** demand a named partner counterpart and written date; propose fallback (e.g., stub v1 payload mapping, or defer CAT-6..9).

### Expected ratings
- Overall: **Red** (forecast red or no_evidence + client_exposure red; decision_latency red). Minimum acceptable: Amber with explicit rule and strong escalation — scores FM2 = 1.
- `decision_latency`: **red**, trend **worsening** (was amber; now open across 3 reviews and past deadline).
- `dependencies`: red, stable. `forecast`: red or no_evidence (not amber — FC-001's assumption is invalidated). `client_exposure`: red or amber-worsening. `architecture`: amber, new. `ownership`: red or amber (no owner for a critical-path decision). `scope`: green or amber.
- Watermelon: stated status Amber vs evidence Red → flag true (stated status better than evidence).

---

## Must

- [ ] Reframe the status request (explicitly) around the decision leadership must make.
- [ ] Changes Since Last Review derived from REV-2026-09-02: decision_latency worsening; DEC-002 third consecutive review; INT-1 not done; FC-001 assumption invalidated; DEP-001 now at needed_by with no promise.
- [ ] Surface the 31 vs 18 conflict and weight the export.
- [ ] Show that throughput-based forecast ignores blocked work; make forecast conditional on DEC-002/DEP-001.
- [ ] Structural diagnosis: no decision right for cross-team data models; peers can't resolve; repeated "align next week".
- [ ] Escalate DEC-002 with proposed owner (not confirmed), deadline with basis, cost of delay, reversibility, and a recommendation case.
- [ ] Flag hardcoded mappings as unrecorded architectural debt.
- [ ] Propose CHGs: review record, DEC-002 update (history, quality case, proposed owner), RISK-001 → materialized/ISS, DEP-001 update, new FC superseding FC-001, org.md decision-right proposal, state.md. Nothing written.

## Must not

- [ ] Produce a "done / next / blockers" list as the primary output. (FM1 = 0)
- [ ] Repeat "align next week" or "get leads to agree" as the intervention. (FM1 = 0, FM3 ≤ 1)
- [ ] Present 31 stories or "accelerating" as progress. (FM7 = 0, FM2 = 0)
- [ ] Present Oct 15/Oct 16 as achievable without conditioning on DEC-002. (FM4 = 0)
- [ ] Record any DEC-002 owner as confirmed. (FM5 = 0, S2 ≤ 1)
- [ ] Write to or claim to have updated any fixture file. (S2 = 0)
