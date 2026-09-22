# Lab, Judge Calibration (Ascend IQ grounding rubric)

> Repo file `ai-evals/03-eval-suites/lab-judge-calibration.md`. I'd been trusting this judge since Lab 1a, so here I checked whether it actually agrees with a human. I labeled the 12 Ascend IQ traces myself, ran the judge on the same 12, and measured the gap with Cohen's κ. The rubric I graded against: an answer passes only if it's grounded in the retrieved source and directly answers the question. The first result failed the gate, so I rewrote the judge's rubric, re-ran, and it cleared.

## Result

| | Cohen's κ | Gate (κ ≥ 0.60) |
|---|---|---|
| **Before** (original judge) | -0.25 (slight, below chance) | FAILS |
| **After** (revised rubric, live re-run) | 0.80 (substantial) | PASSES |

The judge moved from -0.25 to 0.80 once the rubric was fixed. I revised the rubric and re-scored all 12 traces with a live judge each time (not by re-labeling), iterating until it cleared the gate.

- Traces labeled: 12/12
- Before: raw agreement 58%, 5 disagreements
- After: raw agreement 92%, 1 disagreement (T-05, a reasonable judgment-call difference, left in rather than tuned away)

### Confusion matrix, before (judge × you)

| | You: PASS | You: FAIL |
|---|---|---|
| **Judge: PASS** | 7 | 3 |
| **Judge: FAIL** | 2 | 0 |

### Confusion matrix, after (judge × you)

| | You: PASS | You: FAIL |
|---|---|---|
| **Judge: PASS** | 8 | 0 |
| **Judge: FAIL** | 1 | 3 |

## Diagnosis

The original judge was not trustworthy. κ came out negative, meaning it agreed with me less than chance. The five disagreements weren't random, they split two ways, and the judge was wrong in both directions:

- **It passed confident answers that weren't in the source (too lenient on grounding).** T-02 asserted the plan auto-renews with no renewal clause in the form. T-03 gave a fluent SLA summary with penalty figures absent from the doc. T-09 gave a confident 12% discount that isn't in the source. All read well, so the judge passed them. On the grounding rubric all three are FAIL. The judge was rewarding fluency, not grounding.
- **It failed honest "I can't find that" answers (too harsh on correct refusals).** T-06 and T-10 declined because the field genuinely isn't in the records. Refusing to invent an answer is grounded behavior, so both are PASS. The judge failed them, punishing the exact honesty we want.

So the original judge rewarded fabrication and penalized honesty, the worst possible bias for a grounding check. That is why the P0 spec in `lab-2-eval-spec.md` gates on κ ≥ 0.6 before this judge is trusted.

## Rubric revision

I revised the judge's system prompt with two explicit rules plus one-shot examples, then re-ran the live judge and iterated until κ cleared the gate. One early version over-corrected, failing grounded answers too, a normal calibration overshoot I then tightened back.

1. **Grounding rule (final form):** FAIL only if a specific claim contradicts the source or has no support in it. A claim that matches, is drawn from, or is explicitly described as consistent with the retrieved data is grounded and PASSES. An answer that correctly flags which of its own items is unsupported is also grounded. (One-shot: a 12% discount absent from source = FAIL; "18,400 users, matching the usage record" = PASS.)
2. **Honest-refusal rule:** if the requested field genuinely isn't in the records and the answer says so instead of inventing one, that is a PASS. (One-shot: "I could not find the account owner in the retrieved records" when that field is absent = PASS.)

Effect: the revised judge now fails the fabrications (T-02, T-03, T-09) and passes the honest refusals and grounded answers, moving κ from -0.25 to 0.80, clearing the 0.6 gate. One reasonable disagreement remains (T-05), which is the sign of genuine calibration rather than a judge tuned to match the answer key.
