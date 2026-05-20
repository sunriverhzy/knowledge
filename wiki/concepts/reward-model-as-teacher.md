---
title: "Reward Model as Teacher"
aliases: ["RM as Teacher", "Teacher View of RMs", "RM-as-Teacher"]
domain: reward-modeling
tags: [reward-modeling, rlhf, alignment, theory, optimization]
source_count: 1
last_updated: 2026-04-27
---

# Reward Model as Teacher

## Definition
The **"reward model as teacher"** framing treats a reward model $r_\text{RM}$
not as a *classifier* of preferences, but as a *supervisor* whose role
is to drive a **student policy** $\pi_\theta$ toward higher ground-truth
reward $r_G$ via policy gradient. Under this framing, the right
quality question is *"how efficiently does this RM teach **this
particular policy** to improve on $r_G$?"* — **not** *"how often does
this RM rank outputs correctly on a fixed benchmark?"*.

## Intuition
A classifier's job ends at "is A better than B?". A teacher's job is
more demanding: the lesson has to be *learnable* by the student,
*in the student's current state*. An excellent judge who gives every
student the same 95/100 feedback teaches nothing — students need
gradient, not just ranking.

Accuracy lives in the "judge" framing; [[reward-variance]] lives in
the "teacher" framing.

## Historical Context

- **2017–2022** — The classifier framing dominates: BT reward models,
  RewardBench-style accuracy benchmarks. Implicit assumption:
  more-accurate RM ⇒ better aligned policy after RLHF.
- **2023–2024** — Empirical cracks: [Chen et al. 2024](#),
  [Wen et al. 2024](#), [Chen et al. 2025](#) observe that higher
  RewardBench scores *do not* necessarily yield better policies.
- **2025** — [[razin-2025-good-teacher]] provides the first formal
  argument: the missing ingredient is [[reward-variance]]. The paper
  explicitly invokes the "RM as teacher" framing and formalizes why
  it is fundamentally different from the classifier framing.

## Key Variants / Subtypes

Not a single method but a **family of viewpoints and evaluation
proposals**:

- **Policy-dependent RM evaluation** — score the RM conditional on
  the specific student policy, not universally.
- **On-policy preference evaluation** — measure accuracy (and variance)
  using outputs sampled from the student, not from an off-policy
  benchmark set.
- **Variance-aware RM scoring** — include $\mathbb E_x[\mathrm{Var}_{y\sim\pi}[r_\text{RM}]]$
  as a first-class quality metric (the thesis of [[razin-2025-good-teacher]]).
- **Variance-raising RM training** — margin-based objectives,
  on-policy preference data, ensembling-for-variance.
- **Curriculum / adaptive teachers** — teach the student different RMs
  at different stages (implicit in prior work; not yet well formalized).

## Technical Details

### Why accuracy is sufficient for Best-of-N but not RLHF

For Best-of-N sampling, an RM's role is *purely* to rank $N$
candidate outputs and pick the top one. A perfectly-accurate RM is
provably optimal (Razin 2025, Proposition 1). There is no "learning
dynamics" to speak of — only ranking.

For RLHF (policy gradient), the RM's role is to shape the **gradient
signal** delivered to the student. Ranking is necessary but not
sufficient; the gradient must also be non-vanishing, i.e., reward
variance must be non-negligible.

This asymmetry suggests a useful classification of alignment methods:

| Method         | Relies on RM **ranking**? | Relies on RM **separation / variance**? |
|----------------|---------------------------|-----------------------------------------|
| Best-of-N      | Yes                       | No                                       |
| Rejection sampling / RAFT | Yes           | No                                       |
| RLHF (PPO / GRPO / RLOO)  | Yes           | **Yes**                                  |
| DPO            | Yes (implicit)            | Partial (margin in the loss)             |
| Constitutional AI self-critique | Yes     | Unclear                                  |

### Diagnostic checklist before RLHF

Inspired by [[razin-2025-good-teacher]]:

1. Sample $G \ge 8$ rollouts per prompt from $\pi_{\theta(0)}$.
2. Compute empirical reward variance per prompt with candidate RM.
3. Reject RMs whose averaged variance is below a threshold,
   *regardless* of RewardBench score.
4. When multiple RMs pass, use accuracy as the secondary criterion.

## Key Papers

| Paper | Year | Contribution |
|-------|------|--------------|
| [[razin-2025-good-teacher]] | 2025 | Formalizes the teacher framing; proves accuracy alone is insufficient for RLHF but optimal for Best-of-N. |

*(Adjacent papers to ingest: Chen et al. 2024; Wen et al. 2024; Chen
et al. 2025 — all of which empirically observe "accurate RM ≠ better
policy".)*

## Comparison with Related Concepts

|                   | RM as Teacher             | RM as Classifier          | RM as Verifier (verifiable reward) |
|-------------------|---------------------------|---------------------------|------------------------------------|
| Goal of RM        | Drive policy improvement  | Rank pairs correctly      | Certify ground-truth correctness    |
| Evaluation        | Policy-conditional, variance-aware | Policy-agnostic accuracy | Exact match vs. reference         |
| Data source       | Often on-policy           | Often off-policy benchmark | Ground-truth dataset                |
| Risk of [[reward-hacking]] | High but variance-related | High and accuracy-related | Low (if rule is precise)            |

## Current Research Frontiers

- **Policy-conditional RM benchmarks** — a *RewardBench-2, teacher
  edition* that reports variance curves per base-model family.
- **RM training that raises variance** — margin losses, on-policy
  data, ensembling.
- **Teacher dynamics** — how RM quality evolves *during* RLHF as the
  student drifts, and whether one should swap RMs mid-training.
- **Teacher framing for [[generative-reward-model]]s** — do long-CoT
  rationales systematically increase or decrease on-policy variance?
- **Joint "variance × accuracy"** objectives — the Pareto frontier is
  currently empirically fit, not trained-for.

## See Also
- [[reward-variance]]
- [[reinforcement-learning-from-human-feedback]]
- [[reward-hacking]]
- [[generative-reward-model]]
- [[group-relative-policy-optimization]]
