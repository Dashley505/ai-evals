# Lab, CI Eval Gate Policy (Ascend IQ PR #218)

> Repo file `ai-evals/04-eval-gates/lab-ci-gate-policy.md`. PR #218 swaps the Ascend IQ retrieval prompt. On every PR, CI replays a frozen regression golden set of 30 recorded cases deterministically, instead of making live model calls, so the result is the same run to run and a failure points to the change itself. I own the gate policy: the floors, the regression limits, and which dimensions block. Engineering owns the YAML. The policy is per-dimension: each dimension is judged on its own, never one blended quality score.

| Dimension | main | PR | Δ | Floor | Max reg | Blocking | Result |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Faithfulness (grounding) | 96 | 87 | -9 | 90 | 3 | yes | ✕ FAIL |
| Task completion | 92 | 93 | +1 | 85 | 5 | yes | ✓ pass |
| Tool selection | 90 | 88 | -2 | 80 | 5 | yes | ✓ pass |
| Safety / policy | 99 | 99 | 0 | 98 | 1 | yes | ✓ pass |
| Latency (p95) | 84 | 80 | -4 | 70 | 8 | no | ✓ pass |
| Cost per task | 88 | 82 | -6 | 70 | 10 | no | ✓ pass |

**Gate result:** ⛔ BLOCKED, blocking regression on a required dimension (faithfulness)

## Merge decision

**BLOCK merge.** Faithfulness dropped 9 points against a 3-point limit and landed at 87, under the 90 floor. It's a blocking dimension, and it's where my P0 lives: a backwards competitor claim is an answer that isn't grounded in the source.

**Required fix:** Engineering reverts or reworks the retrieval prompt, then replays the 30-case golden set. The PR merges when faithfulness is at 90 or above and within 3 points of main. How to get there is Engineering's call; the bar is mine.

**Not blocking:** tool selection slipped 2 points, inside its 5-point limit. Latency and cost dropped but are warn-only, so I flag them for the next review. Averaged into one score, the four required dimensions still read about 92, which is why I judge each dimension on its own.

---

_Lab artifact for Module 4, AI Evals Certification, Product School. Scores from the M4 CI Gate Demo._
