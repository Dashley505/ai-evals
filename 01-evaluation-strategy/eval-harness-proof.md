# Eval Harness Proof — Module 1

> Repo file `ai-evals/01-evaluation-strategy/eval-harness-proof.md`.
> Eval evidence behind the Eval Results slide of the Module 6 pitch.

## The two prompt versions I ran

**Version A — Concise (executive briefing):**
> You are an executive briefing assistant.
> Summarize in exactly 3 bullet points under 60 words. No preamble, no extra text.

**Version B — Narrative (PR comms):**
> You are a PR communications assistant.
> Write a 100-word narrative summary highlighting wins first, then risks and next steps. Keep a positive tone. No bullets.

Both run on the same source email (Q4 Marketing Campaign Update).

## Eval setup — dataset + judge

- **Dataset:** `Module1Output` — the generated summaries plus the cold-start starter rows.
- **Evaluator:** a Conciseness LLM-as-a-Judge that scores each summary against the golden criteria below.
- **Generator model:** `gemini-3.1-flash-lite`.
- **Judge model:** `gemini-3.6-flash` (newer than the generator). Note: for a true bias-free run the judge should come from a *different* model family than the generator, since a judge scoring its own family tends toward self-preference. Same-family here is a known limitation, not the target state.

## How I beat the cold start — the seed prompt

> Generate 20 example rows for evaluating email-summary quality.
> Each row: an input email + a candidate summary + a first-pass label ("good" or "bad") + a one-line reason.
> Make roughly half concise/faithful ("good") and half verbose or inaccurate ("bad").
> Return it as a markdown table.

This produces ~20 starter rows. The rows are a starting point, not the golden set — the human curation below is what makes it real.

## My definition of good vs bad — the graded part

For Ascend IQ, "good" is defined by **faithfulness first, format second**, because our clients pay for verified data and act on it:

- A summary is **good** only if it preserves every material fact from the source — figures, dates, deadlines, and the direction of each change (up vs down, approved vs blocked). Concise and faithful beats long and complete.
- A summary is **bad** if it drops any material fact, softens a specific number into vague language ("a lot," "soon"), or reverses the meaning of a fact — even if it reads well and fits the length limit.
- A summary is **bad** if it fabricates a cause, number, or detail not in the source. A confident wrong fact is the worst failure for this product; an honest omission with a flag is safer than an invented answer.
- Tone and structure (bullets vs narrative) are style choices, **not** correctness, and do not affect the verdict.

This is the seed of the golden dataset — the human judgment, not the generated rows, is the asset. Rigorous full curation and judge calibration come in later modules.

## Note on the Module 1 outcome

Per the lab guide, Module 1 wires the harness rather than producing a score — with only a row or two the eval has nothing meaningful to evaluate. The deliverable is the working plumbing (dataset + attached judge) and the definition of good above, not a number.
