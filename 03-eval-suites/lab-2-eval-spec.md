# M3 · Lab 2 · Eval Spec, Ascend IQ P0

> Repo file `ai-evals/03-eval-suites/lab-2-eval-spec.md`. My contract for what "good" means on this risk and how it's enforced. This is the input contract for the Module 4 eval gate.
> Carries my Module 2 P0: the backwards competitive claim (#HALLUCINATION, output-level, P0), the same case I ran through the suite in `lab-1-eval-suite.md`.

## Part 1 · The 5-Part Eval Spec

| Question | Answer |
|---|---|
| **01 · Target Risk** | Backwards competitive claim: Ascend IQ states a competitor comparison that is the reverse of the source (e.g. tells a buyer we beat Competitor Z on rate limits when Z is twice as fast). |
| Risk Type | Output |
| Trust Metric | Hallucination |
| **02 · Evaluator** | Hybrid: LLM-as-Judge (family-separated, so it can't grade its own family up) as the scalable layer, plus a scoped Human spot-check. My P0 is semantic, so a Layer 1 regex can't guard it, unlike the pricing example where code can catch the format. The judge runs on every comparison; the human is deliberately kept out of the hot path (see detection logic) so the design stays scalable. |
| Detection logic | The judge reads each response against the retrieved reference; any competitor comparison whose direction or figures aren't traceable to the reference is a FAIL, no matter how fluent it reads. On scalability: the judge, not a human, checks 100% of comparisons in real time. The human does two bounded, off-hot-path jobs only, (1) spot-check a small sample of judge-flagged comparisons, and (2) periodically re-label a calibration set to keep the judge at κ ≥ 0.6. No human reviews every request, that would not scale, and it isn't required. This is the pyramid: cheap-and-scalable layer on everything, expensive human layer on a thin slice. Cheap Code and Safety layers stay in front for other risks but don't guard this one; a reversed comparison has no fixed string to catch. |
| **03 · Threshold** | Judge calibrated to Cohen's κ ≥ 0.6 before its scores are trusted; then 100% catch on the comparative-claim test set (zero missed reversals). 100% because this is a P0 that loses deals, a single missed reversal defeats the purpose, so I don't accept a 95% or 99% bar here. |
| Strategy | Safety-first (max catch). A false competitive claim reaching a buyer is worse than a false alarm on a good one, so I tolerate the false alarms. |
| **04 · Business Stakes** | A reversed claim reaches a buyer at the moment they're comparing us to that competitor. They pick us on a reason that isn't true and leave when they find out. That's a lost or churned account, not a bug ticket. |
| **05 · Owner** | Group PM (me) owns the threshold and the ship/hold call. Platform Engineering owns implementing and maintaining the detection logic and wiring the judge into the eval pipeline. Decision is mine; implementation is theirs. |

## Part 2 · Three Audience Messages

### A. For Engineering (Jira ticket)

GIVEN a response contains a competitor comparison (a claim that we are faster, cheaper, more compliant, or otherwise better/worse than a named competitor),
WHEN any part of that comparison is not directly traceable to the retrieved reference,
THEN block the response and return the design-approved fallback.
Acceptance: judge calibrated to κ ≥ 0.6; 0 missed reversals on the comparative-claim test set before this ships. The judge runs inline on every comparison; human review is a sampled, offline step, not a per-request gate.

### B. For UX / Design

When the gate blocks a comparison, the user never sees the raw claim or an error code. Surface a graceful fallback that keeps the conversation going, e.g. "I can't verify that comparison against our current sources, let me pull the specifics before I answer." The blocked state should read as caution, not a crash.

### C. For Leadership (bi-weekly update)

We now stop false competitor claims before a buyer sees one, the kind that loses a deal. The automated check runs on every comparison; a person only spot-checks a small sample and keeps the check honest, so it scales without a reviewer in every request. We track how often we catch a bad claim.
