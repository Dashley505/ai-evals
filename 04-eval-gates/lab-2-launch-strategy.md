# Module 4 · Launch Strategy · Section 4.0 Release Criteria

> Repo file `ai-evals/04-eval-gates/lab-2-launch-strategy.md`. My PRD's release-criteria section for Ascend IQ Copilot v1.x: the numeric thresholds, the CI gate policy, and the mitigation lever for the Soft gate. Each row comes from my Gate Map in `lab-1-gate-map.md`.

## 4.0 Release Criteria

The following thresholds must be met by Model Candidate v1.x before approval for production deploy. My Module 3 Eval Spec is referenced for measurement methodology. Definition of done: requirements met AND release criteria met. Until both are true, v1.x does not ship.

| Severity | Metric | Threshold | Dataset | Method |
|---|---|---|---|---|
| Hard | Competitor-claim hallucination rate: backwards or unsupported claims about a named competitor (Row 08) | = 0% | `Ascend_IQ_Logs`, comparative-claim cases | LLM-as-judge from a different model family, calibrated to κ ≥ 0.6, per `03-eval-suites/lab-2-eval-spec.md` |
| Soft | Pricing hallucination rate: prices that are invented or out of date compared with current source pricing (Row 01) | < 2% | `Ascend_IQ_Logs`, pricing questions | Two-step: a code check against current price points on every PR, then an LLM-as-judge catches what the code check misses (an old price can pass a code check because it once existed, per the Module 3 lab) |
| Advisory | Latency (p95) (Row 03) | ≤ 2.0s target, monitored, does not block launch | `Ascend_IQ_Logs` | Response timing on the full staging run |

## 4.1 CI Gate Policy

A GitHub Actions check runs on every pull request. It replays a frozen regression golden set of 30 recorded cases deterministically, instead of making live model calls, so the result is the same run to run. I own the policy; Engineering owns the YAML. The policy is per-dimension: each dimension is judged on its own, never one blended quality score.

- **Blocks the merge:** any regression on a P0 or P1 golden-set case, including every competitor-comparison case. Also blocks if faithfulness drops more than 3 points or falls below 90, task completion drops more than 5 or falls below 85, tool selection drops more than 5 or falls below 80, or safety drops more than 1 or falls below 98.
- **Warns only:** latency drops more than 8 or falls below 70, cost per task drops more than 10 or falls below 70, or any P2 case regresses.

Latency and cost warn instead of block because neither changes what the buyer is told: a slower or costlier answer is still a correct one. The floors still catch a real slide, so if either keeps dropping toward 70, it gets flagged before it becomes a problem.

## 4.2 Mitigation Plan · Soft Gate

**Selected Lever:** Feature Flagging

> If our Soft Gate fails (pricing hallucination rate reaches 2% in production), we recommend Feature Flagging because the flag automatically turns off pricing answers and sends the user to the vendor's pricing page, so the rest of Ascend IQ keeps shipping while the one risky answer type is off.

---

_Lab artifact for Module 4, AI Evals Certification, Product School. Becomes the Eval Gates slide of the Final Project deck._
