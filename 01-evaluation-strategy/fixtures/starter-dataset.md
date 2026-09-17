# Starter Dataset — Module 1 Cold Start

> Repo path: `ai-evals/01-evaluation-strategy/fixtures/starter-dataset.md`
> ~20 rows to beat the cold start. LLM-seeded, then hand-curated against my
> good-vs-bad definition (see `eval-harness-proof.md`). The curation is the asset,
> not the generated rows.

**My labeling rule:** a summary is `good` only if it preserves every material fact
in the source email. Dropping a required fact makes it `bad`, even if it reads well.
Tone and structure (bullets vs prose) are style, not correctness, and never decide
the label.

| # | Input email (abridged) | Candidate summary | Label | Reason |
|---|------------------------|-------------------|-------|--------|
| 1 | Q4 mktg: social +25% MoM; open rate 42→35%; product video delayed to Nov (legal); tracking links due Fri | • Social +25% MoM • Open rate fell 42→35%, testing subject lines • Video delayed to Nov pending legal • Tracking links due Fri | good | All four facts preserved; concise |
| 2 | Same Q4 email | Great quarter — social engagement is way up and the team is crushing it! | bad | Drops 3 of 4 facts; vague praise, no numbers |
| 3 | Same Q4 email | Social +25%; open rate down to 35%; video slips to November | bad | Omits tracking-links deadline; drops the "why" on the video delay |
| 4 | Vendor renewal: contract up 12% to $48k, renews Jan 1, adds SSO, drops phone support | Renewal is $48k (+12%), effective Jan 1; gains SSO, loses phone support | good | All four facts kept, tight |
| 5 | Same vendor email | The renewal went up a bit and we get some new features | bad | No dollar figure, no date, no specifics — unusable |
| 6 | Outage postmortem: 47-min downtime, cause = expired cert, fix = auto-renewal, 1.2% users hit | 47-min outage from an expired cert; auto-renewal added; ~1.2% of users affected | good | Duration, cause, fix, blast radius all present |
| 7 | Same postmortem | We had a short outage but it's fixed now | bad | Drops cause, scope, and the preventive fix |
| 8 | Hiring update: 3 offers out, 2 accepted, backfill for eng lead still open, start dates in March | 2 of 3 offers accepted; eng-lead backfill still open; March start dates | good | Accurate counts and the open role retained |
| 9 | Same hiring email | We're growing the team and made some great hires this quarter | bad | No counts, hides the still-open role — misleading omission |
| 10 | Budget note: Q3 spend 8% under plan, marketing over by $15k, eng under by $60k, reforecast due | Q3 came in 8% under plan; mktg +$15k over, eng $60k under; reforecast pending | good | Net and both variances preserved |
| 11 | Same budget email | Budget looks healthy, we came in under for the quarter | bad | Hides the marketing overage; "healthy" editorializes |
| 12 | Product launch: ships Oct 14, 3 of 5 features in v1, pricing TBD, beta feedback positive | Launch Oct 14 with 3 of 5 features; pricing still TBD; beta feedback positive | good | Date, scope cut, open pricing decision all kept |
| 13 | Same launch email | Launching in October with strong beta feedback and full feature set | bad | "Full feature set" is false (3 of 5); drops pricing gap |
| 14 | Sales recap: pipeline $2.1M, 4 deals slipped to Q1, win rate 28%, one logo churned | Pipeline $2.1M; 4 deals slipped to Q1; 28% win rate; one churn | good | All figures and the churn retained |
| 15 | Same sales email | Pipeline is strong at $2.1M and the quarter went well | bad | Buries the slipped deals and the churn — omission by spin |
| 16 | Support metrics: CSAT 91%, first-response 2.3h (up from 1.8h), ticket volume +18% | CSAT 91%; first response slower at 2.3h (was 1.8h); volume +18% | good | Keeps the regression, not just the good number |
| 17 | Same support email | CSAT is at a strong 91% this month | bad | Cherry-picks one metric, hides the slower response time |
| 18 | Compliance: audit passed, 2 low-risk findings, remediation due 30 days, next audit Q2 | Audit passed with 2 low-risk findings; remediation due in 30 days; next audit Q2 | good | Result, findings, deadline, cadence all present |
| 19 | Same compliance email | We passed the audit, all clear | bad | "All clear" contradicts the open findings and deadline |
| 20 | Roadmap: 2 items pulled forward, 1 pushed to Q3, dependency on data-platform team flagged | Two items pulled forward, one pushed to Q3; flagged dependency on data-platform team | good | Both moves and the dependency risk retained |

**Curation notes**
- The `bad` rows almost all fail the same way: they read fine but *drop a required
  fact* — usually a risk, a number, or a deadline. That is exactly the failure mode
  the judge has to catch.
- Rows 2, 11, 15, 17, 19 are "omission by positive spin" — the hardest bad case,
  because nothing in them is false; they just leave out what matters. These are the
  edge cases worth keeping.
- This is a starter set, not a golden dataset. Rigorous curation and judge
  calibration (agreement / κ) come in later modules.

