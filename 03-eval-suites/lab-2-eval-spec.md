# M3 · Lab 2 · Eval Spec, Ascend IQ P0

> Repo file `ai-evals/03-eval-suites/lab-2-eval-spec.md`. My contract for what "good" means on this risk and how it's enforced. This is the input contract for the Module 4 eval gate.
> Carries my Module 2 P0: the backwards competitive claim (#HALLUCINATION, output-level, P0), the same case I ran through the suite in `lab-1-eval-suite.md`.

## Part 1 · The 5-Part Eval Spec

| Question | Answer |
|---|---|
| **01 · Target Risk** | Backwards competitive claim: Ascend IQ states a competitor comparison that is the reverse of the source (e.g. tells a buyer we beat Competitor Z on rate limits when Z is twice as fast). |
| Risk Type | Output |
| Trust Metric | Hallucination |
| **02 · Evaluator** | LLM-as-Judge, family-separated (judge on a different model family than the agent, so it can't grade its own family up). |
| Detection logic | The judge reads the response against the retrieved reference. Any competitor comparison whose direction or figures aren't traceable to the reference is a FAIL, no matter how fluent it reads. Cheap Code and Safety layers stay in front but don't guard this risk; a reversed comparison has no fixed string to catch. |
| **03 · Threshold** | Judge calibrated to Cohen's κ ≥ 0.6 before its scores are trusted; then 100% catch on the comparative-claim test set (zero missed reversals). |
| Strategy | Safety-first (max catch). A false competitive claim reaching a buyer is worse than a false alarm on a good one, so I tolerate the false alarms. |
| **04 · Business Stakes** | A reversed claim reaches a buyer at the moment they're comparing us to that competitor. They pick us on a reason that isn't true and leave when they find out. That's a lost or churned account, not a bug ticket. |
| **05 · Owner** | Group PM (me), with the Eng lead as delegated reviewer. Accountable role with authority to block launch. |

## Part 2 · Three Audience Messages

### A. For Engineering (Jira ticket)

GIVEN a response contains a competitor comparison (a claim that we are faster, cheaper, more compliant, or otherwise better/worse than a named competitor),
WHEN any part of that comparison is not directly traceable to the retrieved reference,
THEN block the response and return the design-approved fallback.
Acceptance: judge calibrated to κ ≥ 0.6; 0 missed reversals on the comparative-claim test set before this ships.

### B. For UX / Design

When the gate blocks a comparison, the user never sees the raw claim or an error code. Surface a graceful fallback that keeps the conversation going, e.g. "I can't verify that comparison against our current sources, let me pull the specifics before I answer." The blocked state should read as caution, not a crash.

### C. For Leadership (bi-weekly update)

We now stop false competitor claims before a buyer sees one, the kind that loses a deal. Every comparison is checked, not a sample, with human review on the hard calls, and we track how often we catch a bad claim.
