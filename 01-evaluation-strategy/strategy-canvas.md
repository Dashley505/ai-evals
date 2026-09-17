# AI Evaluation Strategy Canvas

> Repo file `ai-evals/01-evaluation-strategy/strategy-canvas.md` (the repo is your submission).
> Becomes the Strategy Canvas slide of the final pitch deck you assemble in Module 6.

## 1. Product Strategy, The Context

**Target user:** VP-level Strategists and Product Leaders at Fortune 500 companies who pay a premium for verified market intelligence.

**Key use case:** Rapidly extracting specific, verified insights (e.g., comparing competitor pricing models or summarizing G2 reviews) without manual data digging.

**Value proposition:** Personalized, instant answers based on verified data, dramatically reducing the time spent finding and synthesizing information for high-stakes decisions and strategic roadmaps.

## 2. Measurements, The Execution

**User promise.** For VP-level Enterprise Strategists, Ascend IQ promises to turn plain-language questions into verified, citation-backed answers so they can make high-stakes decisions without manual data digging.

**Top 3 trust metrics:**
- **Hallucination Rate**, % of outputs that are confidently false or fabricated.
- **Latency**, Response speed (P95 / P99). Slow kills engagement.
- **Robustness**, Coherence on messy, adversarial, out-of-scope inputs.

**Why these three:**
- **Hallucination Rate** — clients pay $50k+ for verified data; one confident wrong stat to a VP making a real decision ends the contract.
- **Latency** — named deliberately so we can commit to sacrificing it; speed is real, but it's our release valve, not our priority.
- **Robustness** — Ascend IQ is an agent taking open-ended questions ("summarize the negative G2 reviews"); real users don't type clean queries, and incoherence on messy input breaks trust with unforgiving enterprise accounts.

## 3. Strategic Trade-Offs, The Cost

### Trade-off 1 · Hallucination Rate ↔ Latency
We prioritize Hallucination Rate over Latency because our clients make $1M+ decisions on this data; a confident wrong answer ends a $50k relationship, while a few extra seconds is the acceptable cost of enterprise-grade integrity.

### Trade-off 2 · Hallucination Rate ↔ Robustness
We prioritize Hallucination Rate over Robustness because an honest "I can't verify that" on a messy query is far safer than a confident answer to a question we didn't fully parse; we'll accept narrower coverage on adversarial input to guarantee we are never confidently wrong.

---
_Generated from the AI Evaluation Strategy Canvas, M1 lab tool, AI Evals Certification._
