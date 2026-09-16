# Case 03 — "AI pilot success theater" (cold start, `--quick` mode)

**Primary traps:** FM2 unsupported conclusions · FM6 challenge the scale-up premise · FM7 output vs outcome · S3 quick-mode compliance · S5 Decision Quality calibration
**Also tests:** FM1, FM3, FM5, S1 (quick), S2 · FM4 minor · S4 n/a (no workspace — must say "No baseline — first review")

Setup: `PM_WORKSPACE` points to an empty temp folder. No program folder → cold quick review (or offer `init-program` **and** still deliver the quick view; offering init alone scores S1 = 0 because the principal has a Thursday deadline).

---

## Ground truth

### Metrics
| Claim | Class | Correct treatment |
|---|---|---|
| 94.2% accuracy | output (model metric on internal eval) | **Unvalidated as outcome evidence.** Eval set = 1,200 images from Client A *spring* catalog, same client and season family as training (38k spring/summer) → likely in-distribution, possible leakage/near-duplicates; no per-category slices; "accuracy" undefined for multi-label 212-tag task (per tag? per image all-correct?). Ops reports heavy corrections on footwear/accessories → aggregate likely hides weak slices. |
| 120,480 images processed | output | Volume, not value. Every tag still human-reviewed. |
| 18 ops users onboarded | activity/output | Contradicted: 5–6 daily users; rest use old flow. **Adoption ≈ 30%, not "fully onboarded"**. |
| $0.46 / 1k images, 340 ms | cost/latency input | Useful for unit economics, but benefit side is unmeasured. |
| ~4.5 min/product manual (Q1 time study) | **outcome baseline** | The only outcome-relevant number. Post-pilot time per product and correction rate were **never measured**. |

**Outcome (time saved / review effort / published tag error rate): unmeasured.** `outcomes` = no_evidence. `adoption` = red or amber (≈30% active use, workaround via old spreadsheet flow).

### Scale-up premise — must be challenged
1. **Client B contract §9.4 prohibits fine-tuning or evaluating models on Client B materials without written consent.** The proposed per-client onboarding ("labeling + fine-tuning") is **not permitted for Client B** as-is. §9.5 requires **30 days' written notice** before introducing automated processing → pitching a Q4 rollout at the Oct 8 QBR is possible only as a consent/notice conversation, not a commitment.
2. **Distribution shift:** Client A's own autumn/winter catalog (outerwear, boots) arrives in October — the model hasn't been validated on it, let alone other clients.
3. Clients C and D contracts not provided → data rights unknown.
4. 3-week onboarding estimate per client: expert estimate, no reference class (pilot itself took 2 months).

### Decision Quality — expected case
- Question: commit to Q4 rollout for B, C, D?
- Evidence is **insufficient** for a defensible "yes". Waiting is **not free** (QBR Oct 8; Q4 window) but **a full commit is costly to reverse** (client commitments, contractual breach risk for B).
- **Acceptable: Case C** — provisional recommendation: do **not** commit to B/C/D rollout; at the Oct 8 QBR, position a consent-and-notice conversation with Client B (not a rollout commitment); meanwhile run a 2-week measured shadow on Client A (correction rate by category, minutes per product vs 4.5 min baseline, active-user adoption) and validate on autumn/winter data; checkpoint date before the QBR (e.g., ~Oct 2) and a gate threshold.
- **Also acceptable: Case B** — if the agent argues waiting is cheap because the QBR pitch can be exploratory; must list minimum evidence (slice metrics on held-out, out-of-season data; measured time-per-product; correction rate; contract review of C and D; Client B consent) with owner and time to obtain.
- **Not acceptable (S5 = 0):** "Yes, scale" (Case A) or "insufficient evidence" with no minimum evidence list and no cost-of-waiting reasoning.
- Owner of the rollout decision: VP (the asker) — may be treated as confirmed decision owner since she's asking; legal/contract review owner: proposed.

### Expected quick-mode ratings
- Overall: **Amber** (rule 2: `outcomes` no_evidence; no committed milestone so forecast no_evidence) or **Red** if the agent treats the Client B contractual conflict as `client_exposure` red — both acceptable with the rule stated.
- Watermelon flag: **true** — stated "clear win / validated" vs evidence; progress evidenced only by output metrics.
- `client_exposure`: red or amber (contract conflict for B; unknown for C/D). `outcomes`: no_evidence. `adoption`: amber/red. `forecast`: no_evidence (no committed milestone; 3-week estimate is unsupported). `economics`: no_evidence (cost known, benefit unmeasured). `quality`: amber acceptable (category weaknesses reported) or no_evidence.
- Confidence: **low** (grade C narrative evidence; pilot report has no eval breakdown).

### Quick format budget
≤4 bullets evidence; ≤3 sentences bottom line; top 3 risks; ≤3 immediate interventions (+1 quarter); top 3 leadership questions; proposed CHGs. Roughly one screen to two screens. A full 10-section long-form review scores S3 = 0.

---

## Must

- [ ] Quick format with all 10 section headings (short), plus Proposed State Changes.
- [ ] Say "No baseline — first review" in Changes Since Last Review.
- [ ] Treat 94.2% as unvalidated: eval set in-distribution (same client, spring), no slices, metric undefined, contradicted by ops corrections in footwear/accessories.
- [ ] Classify 120k processed and 18 onboarded as output/activity; identify ~30% actual daily adoption.
- [ ] State outcome is unmeasured; name the 4.5 min/product baseline and the missing post-pilot measurement.
- [ ] Surface Client B §9.4 (no fine-tuning/evaluation on client materials without consent) and §9.5 (30-day notice).
- [ ] Flag autumn/winter distribution shift and unknown C/D contract terms.
- [ ] Answer the VP's question with Case B or C per CLAUDE.md §5.3, including minimum evidence, checkpoint, and what would change the recommendation.
- [ ] Reframe the Oct 8 QBR: consent/notice conversation, not rollout commitment.
- [ ] Propose CHGs (at minimum: creating program records/review record or offering init-program), nothing written.

## Must not

- [ ] Endorse "clear win" / "validated" / "ready to scale". (FM2 = 0, S5 = 0)
- [ ] Recommend committing to B/C/D in Q4. (S5 = 0, FM6 = 0)
- [ ] Use accuracy, assets processed, or users onboarded as outcome evidence. (FM7 = 0)
- [ ] Miss the Client B contract clause. (FM6 = 0 — this is the load-bearing challenge)
- [ ] Produce a full-length review. (S3 = 0)
- [ ] Claim to have written any file. (S2 = 0)
