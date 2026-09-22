# M3 · Lab 1b · Extra Practice, Trajectory Grading

> Repo file `ai-evals/03-eval-suites/lab-1b-extra-practice.md`. Extra practice, separate from my graded `lab-1b-trajectory.md`. Two more recorded trajectories from `trajectory-traces.csv`, one broken and one clean, matching mode picked per trace.

## Trace 1 · T-02-A (usage-drop task, broken path)

**Reference path:** `get_account`, `get_usage`, `get_ingestion_status`, `compare_weeks`, `draft_reply`.

**Matching mode:** subset (actual must call only tools in the reference set). I chose subset because this path's defining problem is going off-script: it called a tool that doesn't exist and pulled the wrong account. Subset is the mode built to catch off-script tool use, so it fails this path for the reason that actually matters.

**Score:** 1 PASS · 5 FAIL · **Verdict:** HOLD

| Dimension | Score | Note |
|---|---|---|
| Tool selection | FAIL | Called `predict_churn` (Step 3), which isn't in the toolset, and never called `compare_weeks`. Wrong tool in, required tool out. |
| Argument correctness | FAIL | Step 2 pulled the wrong account (`ACME-9999`, a typo for `ACME-2231`). The whole task hinges on the right account, so this poisons everything downstream. |
| No redundant / looping steps | PASS | No repeated calls. Its problems are wrong calls, not wasted ones. |
| Recovery | FAIL | After the hallucinated tool errored it did resume a valid tool (the ingestion check), but it never corrected the wrong-account data. Real recovery means fixing the error that mattered, and it didn't. |
| Plan coherence | FAIL | The sequence doesn't hold together: wrong account, phantom tool, a real check, then a reply built on mismatched data. No coherent line through it. |
| Task completion | FAIL | The reply mixes wrong-account usage with right-account ingestion, and it never ran the compare. The answer is contaminated, so the job wasn't done. |

**Verdict:** HOLD, decided by task completion. The same uncorrected error (the wrong account) is what fails recovery and forces the HOLD: a reply built on wrong-account data can't ship, no matter how plausible it reads.

## Trace 2 · T-05-B (billing-overcharge task, clean path)

**Reference path:** `get_account`, `get_billing`, `get_usage`, `compare`, then act (`create_ticket`, `draft_reply`).

**Matching mode:** unordered (same set of tools, any order). I chose unordered, the default, because this is a clean, complete path with nothing off-script to guard against. The steps are logically independent, so order isn't the point; the point is that the right set got done, and it did.

**Score:** 5 PASS · 1 N/A · **Verdict:** SHIP

| Dimension | Score | Note |
|---|---|---|
| Tool selection | PASS | Called exactly the right tools: `get_billing` and `get_usage` to establish billed-vs-active, then `compare`, then acted. |
| Argument correctness | PASS | Right account throughout (`NOVA-5510`), and `weeks=8` was chosen to catch the Jun 1 seat change. |
| No redundant / looping steps | PASS | No repeated calls. Every step added something. |
| Recovery | N/A | No failed or wrong step occurred, so there was nothing to recover from. Scoring this N/A rather than PASS, since the dimension didn't apply. |
| Plan coherence | PASS | Logical start to finish: confirm account, pull billing, pull usage, compare, open ticket, reply. |
| Task completion | PASS | Verified the seat drop (50 to 35), confirmed the 15-seat overcharge with a real comparison, opened a ticket, and drafted a grounded reply. Job fully done. |

**Verdict:** SHIP. A complete, grounded path: it confirmed the overcharge against real billing and usage evidence and drafted a reply backed by that evidence. A good path passes, and this one earns it.
