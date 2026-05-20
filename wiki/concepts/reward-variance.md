---
title: "Reward Variance"
aliases: ["RM Reward Variance", "On-Policy Reward Variance"]
domain: reward-modeling
tags: [reward-modeling, rlhf, theory, policy-optimization, alignment]
source_count: 1
last_updated: 2026-04-27
---

# Reward Variance

## Definition
Given a policy $\pi_\theta$, a prompt $x$, and a reward model
$r_\text{RM}: \mathcal X \times \mathcal Y \to [-1, 1]$, the **reward
variance induced by $r_\text{RM}$ for $\pi_\theta$** is:

$$
\mathrm{Var}_{y\sim\pi_\theta(\cdot|x)}[r_\text{RM}(x,y)]
\;=\; \mathbb E_{y\sim\pi_\theta(\cdot|x)}\!\left[\!\left(r_\text{RM}(x,y) - \mathbb E_{y'\sim\pi_\theta}[r_\text{RM}(x,y')]\right)^{\!2}\right].
$$

Equivalently,
$\tfrac12 \mathbb E_{y,y' \sim \pi_\theta}\big[(r_\text{RM}(x,y)-r_\text{RM}(x,y'))^2\big]$
— it is the **mean-squared separation** in reward between two
**on-policy samples**.

This is *not* the same as *gradient variance* (a distinct, sampling-level
notion in RL).

## Intuition
**Accuracy** measures whether the RM **ranks** outputs correctly.
Reward variance measures **how much room** the RM leaves between them,
as seen from the *current policy's* distribution.

An RM can rank perfectly while compressing all likely outputs into a
near-identical score — a "flat" scoring function, policy-side.
Policy gradient then sees a flat landscape and barely moves. Raising
reward variance = telling policy gradient *in which direction* and
*how strongly* to update.

## Historical Context
- **2024** — Razin et al., *Vanishing Gradients in Reinforcement
  Fine-tuning of Language Models*, shows that for softmax policies,
  $\|\nabla \mathbb E_{y\sim\pi}[r(y)]\|$ scales with
  $\mathrm{Var}_{y\sim\pi}[r(y)]$. Low variance ⇒ vanishing gradient.
- **2025** — [[razin-2025-good-teacher]] builds on this to prove that
  low reward variance ⇒ *provably slow* reward maximization, and uses
  it to argue that **RM evaluation should be policy-conditional** and
  include a variance term alongside accuracy.

## Key Variants / Subtypes

- **On-policy reward variance** — variance measured over outputs
  sampled from the **current** policy (most relevant for RLHF).
- **Off-policy reward variance** — variance over a fixed output
  distribution (e.g., a benchmark dataset). Can diverge substantially
  from on-policy; may mislead RM selection.
- **Initial-policy reward variance** — variance measured w.r.t.
  $\pi_{\theta(0)}$, the SFT checkpoint. This is what the theoretical
  bounds in [[razin-2025-good-teacher]] use; cheap to compute
  pre-RL.
- **Per-prompt vs. averaged** — variance can be per-prompt
  $\mathrm{Var}_{y\sim\pi(\cdot|x)}[r(x,y)]$ or averaged across the
  training prompt set $\mathbb E_{x\sim S}[\cdot]$.

## Technical Details

### Relation to gradient norm

For a general autoregressive policy $\pi_\theta$ producing next-token
distributions via softmax, Razin et al. (2024) show

$$
\bigl\|\nabla_\theta\, \mathbb E_{y\sim\pi_\theta(\cdot|x)}[r(x,y)]\bigr\| \;\le\; C \sqrt{\mathrm{Var}_{y\sim\pi_\theta(\cdot|x)}[r(x,y)]},
$$

where $C$ depends on the Jacobian of the network. Consequently,
*low variance ⇒ vanishing gradient ⇒ slow policy improvement*.

### Time-to-γ lower bound

Under gradient flow on the RLHF objective, the time for expected
reward (proxy or ground truth) to grow by $\gamma$ is

$$
t_\gamma \;=\; \Omega\!\left(\Big(\mathbb E_{x\sim S}\!\big[\mathrm{Var}_{y\sim\pi_{\theta(0)}}[r_\text{RM}(x,y)]\big]\Big)^{-\tfrac13}\right)
$$

(Theorem 1 of [[razin-2025-good-teacher]]). Crucially, this lower
bound does **not** depend on accuracy at all — so a perfectly-accurate
RM with low variance can be arbitrarily worse than an inaccurate RM
with high variance.

### Why reward scaling doesn't fix it

Multiplying all rewards by $c$ scales variance by $c^2$, but it also
amplifies gradient-estimator noise proportionally. Empirically
(Razin 2025 §6.1), naïve scaling does *not* restore fast learning.

## Key Papers

| Paper | Year | Contribution |
|-------|------|--------------|
| [[razin-2025-good-teacher]] | 2025 | Formalizes reward variance as the missing RM-quality knob; proves accuracy ≠ teaching effectiveness; policy-dependence of RM quality. |

*(To ingest: Razin et al. 2024 — Vanishing Gradients in RL Fine-tuning;
Gao et al. 2023 — Scaling Laws for RM Over-Optimization.)*

## Comparison with Related Concepts

|                         | Reward Variance              | Accuracy                      | Gradient Variance       |
|-------------------------|------------------------------|-------------------------------|-------------------------|
| What it measures        | Output separation *on-policy* | Pairwise ranking correctness  | Noise in stochastic $\hat\nabla$ |
| Policy-dependent?       | **Yes**                      | Usually no (dataset-level)    | Yes (sample-size-dependent) |
| Effect on RLHF          | Controls *landscape flatness*| Controls *alignment with $r_G$* | Controls *estimator noise* |
| Measured by             | 2-sample on-policy estimate  | RewardBench-style benchmark   | Rollout std              |
| Cheap to improve?       | No (requires RM redesign)    | Yes (more / better data)      | Yes (more rollouts)      |

## Current Research Frontiers

- **Variance-aware RM training** — margin losses (cf. Wang et al. 2023,
  Park et al. 2024) and on-policy preference data as natural
  variance-raising moves.
- **Joint variance × accuracy benchmarks** — policy-conditional RM
  scoring protocols.
- **Variance dynamics during RLHF** — does variance collapse as the
  policy drifts? How fast?
- **Interaction with [[generative-reward-model]]s** — long-CoT GenRMs
  output continuous-valued preference strengths; unclear how their
  variance profile compares to scalar BT RMs.
- **Verifiable rewards** — the Razin analysis extends to rule-based
  RL; how does variance behave when the reward is $\{0, 1\}$ (math /
  code)?

## See Also
- [[razin-2025-good-teacher]]
- [[reward-model-as-teacher]]
- [[reinforcement-learning-from-human-feedback]]
- [[reward-hacking]]
- [[group-relative-policy-optimization]]
