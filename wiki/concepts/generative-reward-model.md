---
title: "Generative Reward Model"
aliases: ["GenRM", "Generative RM", "CoT-GenRM"]
domain: reward-modeling
tags: [reward-modeling, rlhf, chain-of-thought, generative-reward-model, llm-as-a-judge]
source_count: 1
last_updated: 2026-04-27
---

# Generative Reward Model

## Definition
A **Generative Reward Model (GenRM)** is a reward model implemented as
an LLM that **generates natural-language reasoning before producing a
reward or preference label**. Rather than mapping $(x, y) \mapsto r \in \mathbb R$
discriminatively — as in a [Bradley–Terry reward model](#see-also) —
a GenRM factorizes the task as:

$$
(R, s) \sim G_\theta(\cdot \mid t, x, y_a, y_b),
$$

where $R$ is a generated reasoning trace (a [[chain-of-thought]]) and
$s$ is either a pairwise preference ($s \in \{a,b\}$, or an ordinal
strength label), or a pointwise scalar.

## Intuition
A GenRM is, in effect, an **LLM acting as a judge** — but trained, not
merely prompted, to be one. By emitting a rationale before its verdict,
the GenRM (i) leverages the pretrained LLM's reasoning prior,
(ii) exposes an interpretable trace of why a response was preferred,
and (iii) can exploit **test-time compute** (more thinking → better
judgment) in ways a discriminative head cannot.

## Historical Context
The GenRM paradigm emerges from a convergence of three lines:
1. **RLHF reward modeling** — where BT RMs were the default since
   Christiano 2017 and Ouyang 2022.
2. **LLM-as-a-Judge** (Zheng et al. 2023, *MT-Bench*) — showing that
   *untrained* LLMs can act as evaluators, but with biases.
3. **Chain-of-thought prompting** — showing that reasoning traces
   improve task accuracy.

The first explicit GenRM papers (e.g. Ankner et al. 2024 —
*Critique-out-Loud*; Mahan et al. 2024 — *Generative Reward Models*;
Zhang et al. 2025 — *Generative Verifiers*) trained LLMs with a
next-token-prediction objective over `(rationale, label)` pairs and
demonstrated stronger OOD generalization than BT RMs.

## Key Variants / Subtypes

- **Pointwise GenRM** — outputs a scalar reward for a single response.
  Directly compatible with pointwise RLHF (PPO, GRPO).
- **Pairwise GenRM** — outputs a preference between two responses
  (binary $\{a, b\}$, or an ordinal strength). Requires either
  pointwise conversion (e.g. Elo-style aggregation) or, as in
  [[hong-2025-think-rm]], a purpose-built **pairwise RLHF** pipeline.
- **CoT-GenRM with vertical inference-time scaling** — generate $m$
  independent short rationales, aggregate labels by majority vote.
  Improves self-consistency but plateaus on reasoning-heavy tasks.
- **Long-horizon GenRM** ("Think-RM"-style) — generate a single deep,
  self-reflective rationale (~1k–3k tokens). "Horizontal" rather than
  "vertical" inference-time scaling.
- **Process-Reward GenRM** — emit per-step rewards over a reasoning
  trace; related to process reward models (Lightman 2023; Wang 2023).

## Technical Details

**SFT objective** for a pairwise GenRM, given reasoning trace $\hat R$:

$$
L_{\text{SFT}}(\theta) = \mathbb E\big[-\log G_\theta(s \mid t,x,y_a,y_b, \hat R) - \log G_\theta(\hat R \mid t,x,y_a,y_b)\big].
$$

**Rule-based RL refinement** (e.g. accuracy-only reward):
$r = 1$ if predicted label equals ground truth, $0$ otherwise; use
GRPO or PPO with KL regularization to a reference.

**Consuming GenRM outputs in RLHF**:
- *Pointwise path:* directly plug into PPO/GRPO as $r(x, y)$.
- *Pairwise path:* either convert to scalars (e.g. fit a BT score),
  or use a pairwise advantage derivation. [[hong-2025-think-rm]] proves
  the pairwise GRPO advantage is analytically equivalent to the
  pointwise one under a skew-symmetric preference matrix.

## Key Papers

| Paper | Year | Contribution |
|-------|------|--------------|
| [[hong-2025-think-rm]] | 2025 | Horizontal inference-time scaling for GenRMs; pairwise RLHF pipeline without pointwise conversion; SOTA on RM-Bench sub-10B. |

*(More papers to be added as wiki grows: Mahan 2024 — Generative Reward
Models; Ankner 2024 — Critique-out-Loud; Zhang 2025 — Generative
Verifiers; Yu 2024 — Self-generated critiques.)*

## Comparison with Related Concepts

|                       | Generative Reward Model | Bradley–Terry RM | LLM-as-a-Judge (prompt-only) |
|-----------------------|-------------------------|------------------|------------------------------|
| Training needed?      | Yes (SFT ± RL)          | Yes (classification) | No                       |
| Output                | rationale + label/scalar| scalar           | label (rationale optional)   |
| Test-time compute     | Scales well (vertical/horizontal) | N/A    | Scales via prompting         |
| OOD robustness        | High                    | Low              | Medium (bias-prone)          |
| Interpretability      | High (rationale)        | None             | High                         |
| Data efficiency       | High                    | Low              | N/A (zero-shot)              |
| Susceptibility to [[reward-hacking]] | Lower     | High             | Bias-driven failures         |

## Current Research Frontiers
- **Horizontal inference-time scaling** — single very-long rationales
  beating many short ones on hard tasks (see [[hong-2025-think-rm]]).
- **Process-level GenRMs** — rationales as step-wise reward signals.
- **Non-verifiable tasks** — how to train GenRMs when there is no
  ground-truth label (e.g. creative writing, multi-criteria helpfulness).
- **Pairwise RLHF** — consuming pairwise GenRM outputs without
  pointwise conversion.
- **Self-generated / self-critiqued** training data (Yu et al. 2024).
- **Scaling beyond 8B** — almost all current GenRM studies cap at 7–10B
  due to pairwise-RLHF compute cost.

## See Also
- [[reinforcement-learning-from-human-feedback]]
- [[chain-of-thought]]
- [[reward-hacking]]
- [[group-relative-policy-optimization]]
