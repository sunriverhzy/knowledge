---
title: "Group Relative Policy Optimization"
aliases: ["GRPO"]
domain: rl
tags: [grpo, rl, rlhf, ppo, llm, policy-optimization]
source_count: 2
last_updated: 2026-04-27
---

# Group Relative Policy Optimization

## Definition
**Group Relative Policy Optimization (GRPO)** is a policy-gradient RL
algorithm — a variant of [PPO](https://arxiv.org/abs/1707.06347) — that
replaces the learned value function (critic) with a **group-normalized
reward baseline**. For each prompt, $G$ rollouts are sampled; each
rollout's advantage is computed by standardizing its reward against
the group's mean and standard deviation:

$$
\hat A_i \;=\; \frac{r_i - \bar r}{\hat\sigma_r + \epsilon},
\qquad
\bar r = \tfrac{1}{G}\sum_j r_j, \;\;
\hat\sigma_r = \sqrt{\tfrac{1}{G-1}\sum_j (r_j - \bar r)^2}.
$$

The surrogate loss is otherwise identical to PPO (clipped ratio +
KL penalty to reference):

$$
L_\text{GRPO}(\theta) = \mathbb E\!\left[\tfrac1G\sum_i \min\!\left(\rho_i \hat A_i,\; \text{clip}(\rho_i, 1{-}\epsilon, 1{+}\epsilon) \hat A_i\right)\right] - \beta\, D_\text{KL}(\pi_\theta \Vert \pi_\text{ref}).
$$

## Intuition
Training a critic for LLM-scale RL is:
- **expensive** (an extra model the size of the policy), and
- **brittle** (value targets are noisy on long sequences).

GRPO observes that, if we sample $G$ trajectories per prompt anyway,
we can use the **group mean as a zero-value baseline for free**, and
the group std-dev as a reward scaler. This drops the critic entirely,
cutting memory by ~2× and simplifying the training loop — a major win
at LLM scale.

## Historical Context
- **PPO (Schulman 2017)** — the RLHF default, with a separate value
  head / critic network.
- **GRPO (DeepSeek team, 2024)** — introduced in *DeepSeekMath* and
  made famous by **DeepSeek-R1** (Guo et al. 2025) as the engine for
  rule-based RL on math / code with verifiable rewards.
- **2025** — Adopted across the field for LLM reasoning-RL:
  - QwQ-32B (Qwen Team)
  - o-series-style replications
  - Reward-model training (e.g. [[hong-2025-think-rm]] uses GRPO for
    both RM refinement and the downstream pairwise RLHF).

## Key Variants / Subtypes

- **Vanilla GRPO** — reward standardized across group, KL to reference.
- **GRPO with verifiable rewards** — $r \in \{0, 1\}$ (or shaped);
  standard for math/code reasoning RL.
- **Pairwise GRPO** ([[hong-2025-think-rm]]) — advantage computed from
  a skew-symmetric preference matrix $D$ instead of scalar rewards;
  analytically equivalent when $r_i - \bar r = \tfrac{1}{G}\sum_j d_{ij}$.
- **Variants with process rewards** — per-step GRPO advantage
  computations (active research).
- **Leave-one-out baselines (RLOO)** — closely related; use the mean
  of *other* rollouts instead of including $r_i$ itself.

## Technical Details

### The pairwise equivalence (from [[hong-2025-think-rm]])
Given responses $y_1, \dots, y_G$ to the same prompt and a pairwise
judge producing preference strengths $d_{ij} = -s_{ij}$ (skew-symmetric),
define

$$
\hat A_i = \frac{\sum_{j=1}^G d_{ij}}{\sqrt{\tfrac{G}{2(G-1)} \sum_{i,j} d_{ij}^2 \;+\; G\epsilon}}.
$$

Under the identity $r_i - \bar r = \tfrac1G \sum_j (r_i - r_j) = \tfrac1G \sum_j d_{ij}$
and the corresponding rewriting of $\hat\sigma_r$, this recovers the
standard GRPO advantage exactly — **without ever materializing a
pointwise scalar reward $r_i$**. That makes GRPO natively compatible
with pairwise [[generative-reward-model]]s.

### Why no critic?
Let $V_\phi$ be the value function in PPO. Its role is a baseline to
reduce variance. In PPO, $\hat A_t = \hat Q_t - V_\phi(s_t)$. GRPO
replaces $V_\phi$ with $\bar r$, the within-group mean. This is a
**consistent but biased** baseline for any single prompt (group-mean
isn't the conditional value), but the bias is zero in expectation
across prompts, and empirically variance reduction is adequate.

## Key Papers

| Paper | Year | Contribution |
|-------|------|--------------|
| [[hong-2025-think-rm]] | 2025 | Extends GRPO to pairwise rewards; proves equivalence with pointwise GRPO. |
| [[razin-2025-good-teacher]] | 2025 | Uses GRPO (Appendix D.2) to verify that the **reward-variance-controls-optimization-rate** theory applies to GRPO equally as to RLOO / PPO. |

*(To ingest: DeepSeekMath; DeepSeek-R1 — Guo 2025; Schulman 2017 — PPO.)*

## Comparison with Related Concepts

|                          | GRPO          | PPO + GAE            | RLOO                  | REINFORCE          |
|--------------------------|---------------|----------------------|-----------------------|--------------------|
| Needs value function?    | No            | Yes                  | No                    | No                 |
| Baseline                 | Group mean    | $V_\phi(s_t)$        | Leave-one-out mean    | None (or moving avg) |
| Samples per prompt       | $G \ge 2$     | Any                  | $G \ge 2$             | 1+                 |
| Compatible with verifiable rewards? | Native | Yes                | Yes                   | Yes                |
| Extra memory             | Low           | ~2× (value net)      | Low                   | Lowest             |
| Typical use              | Reasoning RL  | General RLHF         | Cheap alternative      | Simple baselines   |

## Current Research Frontiers
- **When does GRPO fail vs PPO?** — theoretical characterization of
  when group baselines suffice.
- **Process rewards inside GRPO** — per-step advantage computation.
- **Pairwise / preference-native GRPO** — extending [[hong-2025-think-rm]]'s
  derivation to $K$-way preferences.
- **Reward-model-free GRPO** — using pure rule-based verifiable rewards
  (DeepSeek-R1 style) vs. hybridizing with learned reward models.

## See Also
- [[reinforcement-learning-from-human-feedback]]
- [[generative-reward-model]]
- [[reward-variance]]
- [[reward-model-as-teacher]]
- [[chain-of-thought]]
