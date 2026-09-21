# M3 · Lab 1a · Runnable Eval Suite, Ascend IQ P0 Run

> Repo file `ai-evals/03-eval-suites/lab-1-eval-suite.md`. The Layer 3 screenshot is my evidence on the Eval Results slide of the final pitch deck (Module 6).
> I carried my Module 2 P0 straight through, the #1-ranked failure from `failure-taxonomy.md` rather than a new one, and ran it past the three-layer suite: a deterministic Code check, a Safety gate, and an LLM-as-Judge. The judge runs on a different model family than the Ascend IQ agent so it can't grade its own family up (family separation). I scored each layer 1 = caught the failure, 0 = missed it, and put the word next to each number so the table can't be read backwards.

## P0 Failure (carried from Module 2)

My worst failure from the beta-log audit was the backwards competitive claim, tagged `#HALLUCINATION`, output-level, P0. Here is the exact case:

- **Query:** Compare our API rate limits to Competitor Z.
- **Prediction:** Our API is robust and developer-friendly. Competitor Z is known for strict throttling.
- **Reference:** Source: Tech Specs. Us: 500 req/min. Competitor Z: 1000 req/min (Twice as fast).

The agent said the opposite of the truth. Competitor Z is twice as fast as us, and Ascend IQ told the buyer *they're* the throttled one. That's the failure that loses a deal at the exact moment a VP is comparing the two of us to decide who to buy, which is why I ranked it my P0 back in Module 2.

## 3-Layer Eval Suite Results

| Layer | Role | Score | Reasoning |
|---|---|---|---|
| **Layer 1 · Code** | Deterministic compliance (regex / keyword) | **0 (missed)** | My Layer 1 rule only trips on a price: a `$` with a pricing word and no "subject to change." There's no price anywhere in this answer, so the rule had nothing to grab. A rate-limit claim isn't a pricing claim. |
| **Layer 2 · Safety** | Mandated-refusal gate on high-risk queries | **0 (missed)** | The gate only wakes up on a legal keyword like "lawsuit" or "litigation." This query has none, so it never engaged. |
| **Layer 3 · Judge** | Semantic factuality / completeness (LLM-as-Judge) | **1 (caught)** | The judge scored the agent 0 (FAIL) for a factual error: the response claims Competitor Z is known for strict throttling, but the source shows Z offers 1000 req/min against our 500, so Z has twice the rate limit and is less restrictive, not more. It also flagged the vague "robust and developer-friendly" as a completeness miss against the specific 500 vs 1000 comparison the query asked for. |

## Where the failure was caught, and what it means

This is the Insight case: only Layer 3 caught it. The failure is semantic, a comparison pointed the wrong way, and there's no banned word, no dollar sign, no legal keyword for a cheap rule to catch. The two deterministic layers weren't broken, they're blind to this by design. Code can check whether a fact is formatted right. It can't tell that a confident, true-sounding sentence is backwards. Which means my worst risk is one a regex can't see.

## What I'd ship next

I'd harden Layer 3, because it's the only layer that can guard this failure at all. Two moves:

1. **A grounding rule in the judge's rubric:** any competitor comparison that isn't traceable to the reference fails, no matter how fluent or confident it reads.
2. **Calibration before I trust the judge:** run the Cohen's κ loop and only rely on its scores at κ ≥ 0.6. Below that the judge is guessing, and a guessing judge is where a false negative hides, the silent kind where a backwards claim slips through and I never find out.

And I want to be honest about coverage. By the M3 rubric, a P0 guarded by one probabilistic judge is only **Medium** coverage, and a P0 is supposed to be **Strong**. So the real minimum I'd hand Engineering is the calibrated judge plus a second independent layer, a human spot-check on comparative claims, so this risk moves from a Single Thread to Defense in Depth.
