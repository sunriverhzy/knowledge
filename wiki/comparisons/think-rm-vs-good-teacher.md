---
title: "Think-RM vs. What Makes a Good Teacher — Two Views of RM Quality"
domain: [reward-modeling, rlhf, alignment, theory]
papers: ["[[hong-2025-think-rm]]", "[[razin-2025-good-teacher]]"]
last_updated: 2026-04-27
---

# Think-RM vs. What Makes a Good Teacher

Two 2025 papers attack the question *"what makes a reward model good?"*
from **orthogonal** angles. They are complementary rather than
competitive — and their combination sketches a roadmap for
**next-generation RM training**.

## One-Line Summary

- [[hong-2025-think-rm]] — *Make the RM **smarter** (deeper reasoning).*
- [[razin-2025-good-teacher]] — *Even a perfect RM is useless if it
  doesn't induce variance on the student policy.*

The first improves the **judge**; the second reframes the **student–teacher
relationship**. Together: a good RM must (i) judge correctly, (ii)
judge deeply, and (iii) separate outputs that the student actually
emits.

## Comparison Table

| Dimension                   | [[hong-2025-think-rm]] (*Think-RM*)                           | [[razin-2025-good-teacher]] (*Good Teacher*)                       |
|-----------------------------|---------------------------------------------------------------|--------------------------------------------------------------------|
| Type of contribution        | Algorithmic + empirical                                        | Theoretical + empirical                                            |
| Core concept introduced     | Long-horizon [[generative-reward-model]] ("horizontal scaling") | [[reward-variance]] as the missing RM-quality axis                 |
| RM quality knob targeted    | Judgment accuracy + depth                                      | Policy-conditional reward variance                                  |
| Framing of the RM           | Smarter *judge* that reasons longer                            | Effective *teacher* that keeps the objective non-flat              |
| Primary failure mode fixed  | Shallow reasoning → missed subtle errors                       | Flat objective landscape → vanishing gradient                      |
| Proof / evidence            | Empirical: +8% on RM-Bench, better end-policy AlpacaEval2      | Theorems 1–3 (time-to-γ bounds) + 8B-scale experiments             |
| Training recipe             | SFT on long CoT (longest-correct) + rule-based GRPO RL         | No new training algorithm; proposes variance-aware *evaluation* and margin-style objectives as remedies |
| Policy-dependent?           | No (RM judged as universal)                                    | **Yes** — same RM can be great for policy A, bad for B             |
| Method for consuming outputs| Pairwise RLHF (no pointwise conversion)                        | RLOO / GRPO / PPO (standard)                                       |
| Scale tested                | Llama-3.1-8B-Instruct                                          | Pythia-2.8B + Llama-3.2-1B/3B; RMs up to 8B                         |
| Compute cost per RM call    | High (1k–3k token reasoning traces)                            | Same as any scalar RM                                              |
| Benchmarks used             | HelpSteer2/3-Pref, RewardBench, RM-Bench, AlpacaEval2          | RewardBench (for RM selection) + UltraFeedback RLHF                |

## Detailed Analysis

### Where Think-RM excels
- **Difficult, reasoning-heavy RM tasks.** RM-Bench Math (+12%) and
  RewardBench Chat-Hard (+10%) show that long-horizon reasoning in the
  judge resolves subtle errors that scalar BT RMs miss.
- **Pairwise-native RLHF.** Removing pointwise conversion is a real
  engineering win for any codebase that already speaks GRPO.
- **Data efficiency.** 6k training examples, sub-10B model, SOTA on
  RM-Bench — a counter-point to "throw more data at it".

### Where Good Teacher excels
- **Diagnostic power.** Any practitioner can measure on-policy reward
  variance *before* paying for RLHF and reject a bad-teacher RM early.
  Think-RM's contribution does not address this question at all.
- **Explanatory scope.** Explains *why* empirically-observed failures
  of RewardBench-as-oracle occur — not just "accurate RMs sometimes
  fail" but a precise mechanism.
- **Method-agnostic.** The theorems apply to RLOO, PPO, GRPO and
  verifiable-reward RL equally; Think-RM is tied to pairwise GRPO.

### The Blind Spots

- **Think-RM never measures reward variance.** Its comparison to
  CoT-GenRM + vertical voting is on *judgment accuracy*, not on
  on-policy separation. A long-CoT judge *could* in principle
  compress its outputs onto a narrow score range and still win
  benchmarks while being a worse teacher. The Good-Teacher paper
  would predict exactly this possibility, and it remains untested.
- **Good Teacher does not explore [[generative-reward-model]]s.**
  Its RMs are scalar BT-style models. Whether long-CoT GenRMs
  systematically help or hurt reward variance is unknown — and is
  the obvious next experiment.
- **Neither paper studies variance dynamics *during* RLHF.** Both
  analyses are strongest at $t=0$ (initial policy) and weakest in
  mid-training.

### Hybrid / Follow-up Directions

These papers are begging for a joint follow-up. Plausible next work:

1. **Variance-aware Think-RM** — train a long-CoT GenRM with a
   *margin-augmented* objective that penalizes compressed reward
   distributions on a calibration policy.
2. **On-policy long-CoT preference data** — Think-RM's SFT uses
   longest-correct CoT from QwQ-32B. Sampling from the student policy
   instead would jointly raise variance (per Good-Teacher) and match
   Think-RM's deep-reasoning goal.
3. **Best-of-N vs RLHF switch by variance** — use Think-RM for
   Best-of-N at inference (where accuracy alone is optimal), but
   screen for variance before deploying it in RLHF.
4. **Policy-conditional RM benchmark for GenRMs** — extend RM-Bench
   with on-policy variance curves per base-model family; current
   GenRM rankings are all policy-agnostic.

## Recommendation

**Choose Think-RM (and ingest its recipe) when:**
- Your RM is a clear bottleneck on reasoning-heavy tasks (code, math,
  long multi-turn).
- You are willing to pay for ~1 k-token judge traces per call.
- You are using pairwise GRPO RLHF and can plug in the pairwise
  advantage derivation cleanly.

**Apply Good-Teacher insights when:**
- You have RewardBench-high RMs that still produce disappointing RLHF
  results — measure on-policy reward variance; you probably have a
  teacher mismatch.
- You are choosing among multiple candidate RMs for a given base model
  — screen by variance first, accuracy second.
- You are designing a new RM training objective — include a margin /
  separation term, not only a BT log-likelihood.

**Combine both when:**
- You are building a frontier RLHF stack and want simultaneous gains
  in judge depth *and* teaching effectiveness. Train a long-CoT GenRM
  with margin losses on on-policy preference data sampled from your
  target student model. This is currently an **open research direction**
  — neither paper has demonstrated it.

## See Also
- [[generative-reward-model]]
- [[reward-variance]]
- [[reward-model-as-teacher]]
- [[reinforcement-learning-from-human-feedback]]
- [[group-relative-policy-optimization]]
