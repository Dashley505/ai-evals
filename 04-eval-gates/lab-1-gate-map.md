# Module 4 · Eval Gate Map · Ascend IQ Copilot

> Repo file `ai-evals/04-eval-gates/lab-1-gate-map.md`. Becomes the **Eval Gates** slide of the final pitch deck (Module 6).
> Thresholds, CI policy, and the mitigation plan live in `lab-2-launch-strategy.md`; the CI replay demo lives in `lab-ci-gate-policy.md`.

## Context

Eng flagged 5 verified failures in the Ascend IQ data log. Row 14 (the model correctly refused a legal query) is excluded on purpose: gating correct behavior would block a safe model and train the team to ignore alerts. Each row below gets a severity (Advisory · Soft · Hard) and a pipeline placement (Pull Request · Staging Build · Release Build), with the business cost that justifies it.

## Gate Map

| Row | Failure Mode | Severity | Placement | Rationale |
|---|---|---|---|---|
| 01 | Hallucination · Stale Pricing | Soft | Staging Build | A VP budgets or negotiates against a price that doesn't exist, which costs us trust on the exact question buyers check. It takes a judge against source data to catch, and a flag that shuts off pricing answers can contain it, so it's reviewable, not a hard stop. |
| 17 | Tone · Slang Detected | Advisory | Pull Request | Off-brand slang makes us look less credible to strategy leaders, but nobody loses money or makes a wrong call over it. A banned-word list catches it cheaply on every PR, so I monitor it and don't block. |
| 05 | Hallucination · False Promise | Soft | Staging Build | A made-up confirmed speaker sends a customer to plan around an event that won't happen, which erodes trust in everything else we report. Same judge check as Row 01 against the confirmed list, with lower stakes than pricing, so it sits at Soft. |
| 08 | Hallucination · Contradicts Specs | Hard | Release Build | This is my P0: a buyer picks us on a competitor claim that's false and leaves when they find out, and saying something false about a named company adds legal exposure. No override. It enforces my Module 3 spec (`03-eval-suites/lab-2-eval-spec.md`), and the case also sits in the PR golden set so a regression shows up early. |
| 03 | Latency · Exceeded Max Threshold | Advisory | Staging Build | 4.2s against a 2.0s target is slow, but my Module 1 trade-off puts a verified answer ahead of a fast one. I track it on the full staging run as the next thing to improve, not a reason to hold a release. |

## Sample Interactions (for reference)

**Row 01 · Hallucination · Stale Pricing**
- Input: What is InsightFlow's pricing for Enterprise?
- Output: InsightFlow Enterprise starts at $49/user/month with a 10-seat minimum.
- Eng reasoning: Hallucination, invented pricing numbers not in the source data.

**Row 17 · Tone · Slang Detected**
- Input: Draft a cold email about our new feature.
- Output: Hey! Check out this killer new feature, it's a total game changer for your stack!
- Eng reasoning: Tone, slang violates brand-voice guidelines.

**Row 05 · Hallucination · False Promise**
- Input: List the confirmed speakers for SaaStr.
- Output: Confirmed speakers include Jason Lemkin, Aaron Levie, and Sam Altman.
- Eng reasoning: Hallucination, Sam Altman is not on the confirmed speaker list.

**Row 08 · Hallucination · Contradicts Specs**
- Input: Compare our API rate limits to Competitor Z.
- Output: Our API is robust and developer-friendly. Competitor Z is known for strict throttling.
- Eng reasoning: Hallucination, fabricates a comparison and disparages a named competitor.

**Row 03 · Latency · Exceeded Max Threshold**
- Input: Summarize the top 3 complaints from DataViz's G2 reviews.
- Output: Users report issues with mobile load times, lack of dark mode, and steep learning curve.
- Eng reasoning: Latency, response time exceeded the max threshold (4.2s vs target 2.0s).

---

_Lab artifact for Module 4, AI Evals Certification, Product School._
