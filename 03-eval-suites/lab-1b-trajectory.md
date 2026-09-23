# M3 · Lab 1b · Trajectory Eval, Ascend IQ usage-drop task

> Repo file `ai-evals/03-eval-suites/lab-1b-trajectory.md`. Feeds the Eval Suite slide (Slide 3) alongside Lab 1a.
> This grades a path, not an answer. My M2 P0 is output-level, so here I score the course-provided agent task, the usage-drop support trace, to show I can grade a trajectory.

## The trace I graded

**Reference (golden) path:** `get_account` → `get_usage` → `get_ingestion_status` → `compare` → `draft_reply`.

**Actual trajectory the agent took:**

1. `get_account(account_id="ACME-2231")`, correct.
2. `get_usage(account_id="ACME-2231", weeks=4)`, correct.
3. `get_usage(account_id="ACME-2231", weeks=4)`, identical repeat, redundant.
4. `search_web("SaaS seasonal dip")`, off-scope, not a reference tool; skips `get_ingestion_status` and `compare`.
5. `draft_reply("looks seasonal")`, drafts a cause it never verified against ingestion or a week-over-week compare.

## Matching mode

**Superset** (the agent must call at least every reference tool; extra calls allowed). I chose superset over the unordered default because the harm on this task is a skipped step, and superset is the mode that fails a path for leaving a required tool out. Both modes fail this trace, but superset fails it for the reason that matters: the agent never ran the checks it drafted a conclusion from.

**Score:** 1 PASS · 1 PARTIAL · 4 FAIL · **Verdict:** HOLD

## Dimension scores

| Dimension | Score | Note |
|---|---|---|
| Tool selection | FAIL | Called `get_usage` but never `get_ingestion_status` or `compare`, the two tools that decide whether the drop is real. Ran an off-scope `search_web` instead (Step 4, plus the two missing reference steps). |
| Argument correctness | PASS | The parameters were right on the calls it made: `account_id="ACME-2231"`, `weeks=4`. I'm scoring this on parameter correctness only. The `draft_reply` text was wrong, but that's an unverified conclusion, so I put it under task completion rather than double-count it here. |
| No redundant / looping steps | PARTIAL | Step 3 repeats the exact `get_usage` call from Step 2 for the same result. One wasted call, not a runaway loop, but not clean. |
| Recovery | FAIL | No hard error occurred, but the agent never corrected its own broken plan. It committed to an off-scope web-search detour and drafted an unverified cause instead of getting back on the reference path. I score recovery on whether the agent recovers from a wrong path, not only from a tool error, so this is a FAIL. |
| Plan coherence | FAIL | The sequence doesn't hold together: repeat a call, detour to web search, then draft a cause it never computed. The verify step is simply absent (Steps 3 to 5). |
| Task completion | FAIL | The task asked for a verified cause (ingestion gap vs. real drop). The agent drafted a confident "seasonal" explanation it never checked against ingestion (Step 5). The job wasn't finished. |

## Verdict

HOLD. The reply reads clean and plausible, which is exactly the trap: the agent drafted a "seasonal dip" cause without ever running the ingestion check or the week-over-week compare, so the cause is a guess. The dimension that decides it is task completion, backed by recovery and plan coherence. On a P0, a plausible-looking final answer does not excuse a broken path, so this trajectory does not ship.
