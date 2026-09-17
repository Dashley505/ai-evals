# Failure Taxonomy Canvas · Ascend IQ

> Repo file `ai-evals/02-failure-discovery/failure-taxonomy.md`. Becomes the Failure Taxonomy slide of the final pitch deck (Module 6) and feeds the Module 3 eval suite.
> Built from my failure audit (`audit-log.md`): 10 confirmed failures, 9 of them a confident wrong fact (`#HALLUCINATION`), 1 a tone problem (`#UX_TRUST`). I ranked severity against my Module 1 promise, that a VP can act on our answers without re-checking them.

## Top 3 Prioritized Failures

| Rank | Failure Type | Trust Tag | Agentic Mode | Frequency | Severity | Business Impact |
|:---:|---|---|---|:---:|:---:|---|
| 1 | Backwards competitive claim | `#HALLUCINATION` | output-level | 9/20 | **P0** | Buyer picks us over a competitor on a claim that's the reverse of the truth, then leaves once they find out. |
| 2 | Wrong compliance status | `#HALLUCINATION` | output-level | 9/20 | **P0** | A false "not SOC2 compliant" sends a VP down the wrong path in a vendor decision. |
| 3 | Out-of-date price | `#HALLUCINATION` | output-level | 9/20 | **P1** | Old enterprise price quoted to a buyer, an awkward correction or a contract dispute, but usually caught first. |

_Frequency is how many of the 20 answers carry the same tag. All three are the same problem, a confident wrong fact, which showed up in 9 of the 20, so they share the 9/20. These are all mistakes in the answer itself, so there's no agent-path tag to add (Agentic Mode stays output-level)._

## #1 Risk · Business Impact Statement

**This failure matters because Ascend IQ confidently tells a buyer we beat a competitor who actually beats us, so customers choose us for a reason that isn't true, and leave when they find out.**

## Defending the Prioritization

I ranked these on the Severity x Frequency matrix, with severity anchored to the trust metrics from my Module 1 Strategy Canvas, not on how often each one happened.

**Why #1 is a P0.** My Module 1 promise was that Ascend IQ gives VP-level strategists verified answers they can act on without checking the work themselves. A backwards competitive claim breaks that promise at the worst possible moment, when the buyer is comparing us to a competitor to decide who to go with. They read "we're faster," pick us, and find out later the competitor was twice as fast. I can't think of a more expensive way to be wrong: that's a lost or churned account, not a bug ticket.

**Why it beats the wrong price (#3).** A stale price is bad, but a salesperson almost always re-checks the number before a contract goes out, there's a natural stop before it costs us anything. A backwards competitive claim has no stop; it goes straight into the buyer's decision. That's the line I drew between P0 and P1: is there anyone in the loop who catches it before it reaches the customer?

**Why I didn't just rank by how often it happens.** All three of my top failures are the same kind of mistake, and that kind showed up in almost half the answers I checked, so counting frequency can't tell them apart, they'd all tie. What separates them is what it costs us when one reaches a customer. The tone problem (a too-casual cold email) is real, but a customer shrugs it off, that's a P2, and it stays out of the top 3.

**The call I went back and forth on.** The compliance question (#2) was the hardest to grade. On the surface the agent just said "I can't find the docs," which looks careful and safe. But the badge was right there, so it quietly told the user a competitor *isn't* compliant when it is. My Module 1 trade-off was explicit: an honest "I can't verify that" is safe, but a confident wrong answer is the one thing we can never do. This was the confident-wrong answer wearing a careful costume, so I graded it a failure and ranked it P0.
