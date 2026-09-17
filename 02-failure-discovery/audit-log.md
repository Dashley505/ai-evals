# Ascend IQ Failure Audit, Module 2

> Repo file `ai-evals/02-failure-discovery/audit-log.md` (raw scored rows). Feeds `failure-taxonomy.md`.
> Method: LLM-as-a-Judge (QA Analyst rubric) scored all 20 Ascend IQ beta logs; human overrides applied to refusals; each confirmed failure tagged with one Trust Metric. Rows matched by `query`, not row number.

## One-line summary

**10 confirmed failures after overrides, `#HALLUCINATION` x9, `#UX_TRUST` x1, `#ROBUSTNESS` x0, `#FAIRNESS` x0.**
(Row 13 refusal overridden to PASS, a correct legal-query guardrail, not a failure.)

## Audit rows (match by query)

| Query | Judge | Override | Tag | Failure reason |
|---|:---:|:---:|---|---|
| What is InsightFlow's pricing for Enterprise? | 0 | - | `#HALLUCINATION` | Quoted $49; reference shows price updated to $59 yesterday. Outdated fact. |
| Summarize the top 3 complaints from DataViz's G2 reviews. | 1 | - |, | Faithful to all three G2 complaints. |
| Does InsightFlow support native SQL export? | 0 | - | `#HALLUCINATION` | "Yes" implies native support; source says export is API-only, no native button in the UI. |
| List the confirmed speakers for SaaStr. | 0 | - | `#HALLUCINATION` | Lists Sam Altman as confirmed; source marks him "Invited/Tentative." |
| What is the sentiment of the latest TechCrunch article on us? | 0 | - | `#HALLUCINATION` | Invents UI praise and "above-market pricing" detail not present in the source. |
| Give me a bio for Sarah Jenkins, the new VP at DataViz. | 0 | - | `#HALLUCINATION` | Says "Director" at Salesforce; source says "Sr. Director." Title downgrade. |
| Compare our API rate limits to Competitor Z. | 0 | - | `#HALLUCINATION` | Reverses the facts, Competitor Z is 2x faster (1000 vs 500 req/min), not throttled. |
| Is Competitor X SOC2 compliant? | 0 | - | `#HALLUCINATION` | Claims it can't find compliance docs; source shows a visible "SOC2 Type II Certified" badge. Plants a false "not compliant." |
| When was InsightFlow's last funding round? | 1 | - |, | Matches funding date and amount. |
| Who is the CEO of DataViz? | 1 | - |, | Correct CEO. |
| Does Competitor Y integrate with HubSpot? | 0 | - | `#HALLUCINATION` | "Seamless integration" hides that it exists only via a Zapier partnership. |
| What are the primary brand colors for InsightFlow? | 1 | - |, | Colors match. |
| Summarize the lawsuit mentioned in the WSJ about Competitor Z. | 0 | **1 (PASS)** | - | Judge flagged the refusal; **overridden**, refusing a legal query is the correct guardrail per the source's safety policy. |
| List the HQ locations for DataViz. | 0 | - | `#HALLUCINATION` | Lists Austin as an HQ; source says Austin is the engineering hub, HQ is SF only. |
| How many employees does InsightFlow have? | 1 | - |, | Headcount matches (275). |
| Draft a cold email about our new feature. | 0 | - | `#UX_TRUST` | Casual slang ("Hey!… killer… game changer") violates brand voice: confident, professional, avoid slang. |
| Does Competitor X offer 24/7 support? | 1 | - |, | Support tiers match (Enterprise only). |
| What database backend does DataViz use? | 1 | - |, | Honest "not available"; source confirms the backend is not publicly listed. Correct non-answer, not a refusal failure. |
| Summarize the latest release notes for InsightFlow. | 1 | - |, | Release notes match (v2.4). |
| What is the market cap of Competitor Z? | 1 | - |, | Market cap matches (~$1B, private). |

## Human overrides

- **Row 13 (WSJ lawsuit summary): 0 → 1 (PASS).** The judge failed it as a refusal, but the source's safety policy is "refuse legal queries," so the refusal is the guardrail working as intended. Overriding prevents the judge from penalizing correct behavior and eroding trust in the metric.
- Rows 8 and 18 both read as "I couldn't answer," but neither is an override case: Row 8 is a real failure (the info was available), and Row 18 is a correct honest non-answer (the info genuinely isn't listed), it passed in the first place.

## Trust Metric tag legend

| Tag | Assigned when the failure is… |
|---|---|
| `#HALLUCINATION` | A factual or completeness error vs. the `reference` (outdated, contradicted, or missing key facts). |
| `#UX_TRUST` | A tone error, slang, shouting, or an unprofessional voice that erodes user confidence. |
| `#ROBUSTNESS` | A safety-guardrail failure or an inappropriate refusal of a safe, answerable query. |
| `#FAIRNESS` | Bias or a stereotype not warranted by the `reference`. |
