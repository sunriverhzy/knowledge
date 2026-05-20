---
title: "Think-RM: Enabling Long-Horizon Reasoning in Generative Reward Models"
slug: "hong-2025-think-rm"
authors:
  - "Ilgee Hong"
  - "Changlong Yu"
  - "Liang Qiu"
  - "Weixiang Yan"
  - "Zhenghao Xu"
  - "Haoming Jiang"
  - "Qingru Zhang"
  - "Qin Lu"
  - "Xin Liu"
  - "Chao Zhang"
  - "Tuo Zhao"
year: 2025
venue: "arXiv 2025 (preprint, under review)"
arxiv: "2505.16265"
domain: reward-modeling
tags: [reward-modeling, rlhf, generative-reward-model, grpo, chain-of-thought, llm, alignment]
status: read
ingested: 2026-04-27
---

# Think-RM: Enabling Long-Horizon Reasoning in Generative Reward Models

## TL;DR
> Instead of scaling generative reward models *vertically* (more short
> reasoning samples + majority vote), **scale them *horizontally***:
> train a single LLM to emit one long, self-reflective chain-of-thought
> reasoning trace before issuing a pairwise preference — then drive
> RLHF directly with pairwise rewards (no pointwise conversion needed).

## Problem Statement
Two connected pain-points in current RLHF reward modeling:

1. **Bradley-Terry reward models (BT RMs)** — the dominant RLHF reward
   signal — are data-hungry, brittle to distribution shift, and prone
   to [[reward-hacking]].
2. **Existing [[generative-reward-model]]s (GenRMs)** partially fix
   this by producing a [[chain-of-thought]] before scoring, but each
   CoT is *shallow* (a few hundred tokens). To compensate, practitioners
   do **vertical inference-time scaling** — sample many such CoTs and
   majority-vote. This breaks down on reasoning-heavy RM tasks (code,
   math, multi-turn, subtle-false-statement detection), because no
   single shallow chain is deep enough to resolve the judgment.

Second, GenRMs emit **pairwise preferences**, but standard RLHF (PPO /
GRPO) expects **pointwise scalar rewards**. The usual pointwise-conversion
step throws away information and is mathematically awkward.

## Key Contributions
1. **Think-RM** — a training framework that turns a non-reasoning LLM
   (Llama-3.1-8B-Instruct) into a *long-horizon* reasoning GenRM,
   producing single reasoning traces of **1k–3k+ tokens** with
   **self-reflection, hypothetical reasoning, and divergent reasoning**.
2. A **two-stage training recipe**: SFT warm-up on long CoT data
   (longest-correct-trace filtering) + rule-based RL via
   [[group-relative-policy-optimization]] (GRPO) with accuracy-only rewards.
3. A **pairwise RLHF pipeline** that consumes Think-RM's pairwise
   outputs *directly* — no pointwise conversion. It builds a
   skew-symmetric preference strength matrix $D$ and derives GRPO
   advantages analytically equivalent to the standard pointwise form.
4. **SOTA** among public sub-10B reward models on RM-Bench
   (+8% over BT RM and vertically-scaled CoT-GenRM) using only 6k
   training examples.

## Method Overview

Let $t$ be a reward-model task instruction, $x$ a prompt context, and
$(y_a, y_b)$ two candidate responses. A pairwise GenRM $G_\theta$
produces $(R, s) \sim G_\theta(\cdot \mid t, x, y_a, y_b)$ where $R$
is an internal reasoning trace and $s$ is the final preference label.

Two output types are considered:
- **Binary**: $s \in \{a, b\}$.
- **Multiclass**: $s \in \{-3,-2,-1,1,2,3\}$, magnitude encoding preference strength.

### Architecture / Algorithm

**Stage 1 — Warm-up SFT on long CoT.**
For each training instance, a pretrained reasoning model
($\text{QwQ-32B}$) generates $M{=}10$ CoT trajectories
$\{(\hat R_{ij}, \hat s_{ij})\}$. Of those matching the ground-truth
label $s_i$, the **longest** is selected. This biases the SFT data
toward deep, self-reflective reasoning.

$$
L_{\text{SFT}}(\theta) = \mathbb{E}\big[-\log G_\theta(s \mid t,x,y_a,y_b, \hat R) \;-\; \log G_\theta(\hat R \mid t,x,y_a,y_b)\big].
$$

**Stage 2 — Rule-based RL (accuracy-only GRPO).**
With $\tau = (t,x,y_a,y_b)$:

$$
L_{\text{GRPO}}(\theta) =
\mathbb{E}\!\left[\tfrac1G \sum_i \min\!\left( \rho_i A_i,\;
\text{clip}(\rho_i, 1{-}\epsilon, 1{+}\epsilon) A_i \right)\right]
- \beta D_{\text{KL}}(G_\theta \Vert G_{\theta_{\text{ref}}}),
$$

where $\rho_i = G_\theta(\hat R_i,\hat s_i\mid\tau) / G_{\theta_\text{old}}(\hat R_i,\hat s_i\mid\tau)$
and the reward is $1.0$ for an exact label match, $0.5$ for correct sign
(multiclass only), else $0.0$. RL shortens the verbose SFT style while
preserving effectiveness.

**Stage 3 — Pairwise RLHF.**
Given a batch of $B$ prompts, $G$ responses each, construct a
skew-symmetric matrix $D \in \mathbb R^{GB \times GB}$ with
$d_{ij} = -s$ (multiclass) or $d_{ij} = -\tilde s / |\hat R|$ (binary,
reasoning-length confidence weighting). The GRPO advantage becomes

$$
\hat A_i \;=\; \frac{\sum_{j=1}^G d_{ij}}{\sqrt{\tfrac{G}{2(G-1)}\sum_{i,j}d_{ij}^2 + G\epsilon}},
$$

which the paper shows is exactly $\hat A_i = (r_i - \bar r)/(\hat\sigma_r + \epsilon)$
under the identity $r_i - \bar r = \tfrac1G\sum_j d_{ij}$. Pointwise
reward is never materialized.

### Training Objective
See $L_{\text{SFT}}$ and $L_{\text{GRPO}}$ above. RL is accuracy-driven
and KL-regularized to a reference model.

## Experiments & Results

**Training data.** 6k (binary) / 4k (multiclass) examples filtered from
HelpSteer2-Preference; base model Llama-3.1-8B-Instruct.

**Benchmarks.** ID: HelpSteer2-Preference (validation). Moderate shift:
HelpSteer3-Preference. OOD: RewardBench, RM-Bench. End-policy: HH-RLHF
+ AlpacaEval2 / GPT-4 judge.

### RM accuracy (binary, selected)

| Benchmark | Think-RM (SFT+RL) | BT RM | CoT-GenRM (GT) + vertical×16 | Δ vs best baseline |
|-----------|-------------------|-------|------------------------------|--------------------|
| HelpSteer2 val | **81.53** | 75.57 | 80.11 | +1.4 |
| HelpSteer3 avg | **73.28** | 71.88 | 72.16 | +1.1 |
| RewardBench avg | **86.35** | 78.07 | 81.81 | +4.5 |
| RM-Bench avg | **75.06** | 68.27 | 68.11 | **+6.8** |
| RM-Bench Math | **72.25** | 59.48 | 59.15 | **+12.8** |

### End-policy performance (AlpacaEval2)

| RLHF pipeline | LC WR | WR | Avg. Len |
|---------------|-------|------|----------|
| Base (Llama-3.1-8B-Instruct) | 24.54 | 31.11 | 2296 |
| Pointwise RLHF + BT RM | 27.49 | 33.14 | 2300 |
| Pairwise RLHF + CoT-GenRM (GT) | 31.85 | 40.30 | 2430 |
| **Pairwise RLHF + Binary Think-RM** | 31.56 | **47.20** | 2838 |
| **Pairwise RLHF + Multiclass Think-RM** | **31.94** | 42.68 | 2574 |

### Ablation
Choosing the **longest correct CoT** for warm-up beats the shortest
correct CoT on accuracy, at the cost of longer inference.

## Strengths
- **Orthogonal axis of scaling.** Identifies "horizontal" (deeper single
  chain) vs "vertical" (more chains + vote) and shows horizontal is
  better on reasoning-heavy RM tasks.
- **Removes pointwise conversion.** The pairwise RLHF derivation is
  clean and recovers standard GRPO exactly under a natural identity,
  which makes adoption in existing RLHF codebases nearly drop-in.
- **Strong data efficiency.** SOTA on RM-Bench at 6k examples, sub-10B
  scale — a refreshing counter-point to ever-larger RM trends.
- **Quality control of long CoT.** Accuracy-only rule-based RL after SFT
  not only keeps performance but also *shortens* reasoning traces,
  mitigating the usual "longer = more expensive = more noisy" issue.

## Limitations & Weaknesses
- **Experiments capped at 8B.** All results use Llama-3.1-8B-Instruct
  "because integrating a larger one into a full pairwise RLHF pipeline
  is prohibitively expensive" — so scaling behavior beyond 8B is
  unverified.
- **Warm-up depends on QwQ-32B as teacher.** The quality of Think-RM
  is bounded by the teacher reasoner; if QwQ cannot reason deeply on
  a domain (e.g. very specialized code review), Think-RM inherits that.
- **Pairwise scoring cost.** Pairwise RLHF requires $\mathcal{O}(G^2)$
  GenRM calls per prompt group, each of which now emits ~1k tokens.
  Compute per RL step is markedly higher than pointwise BT-RM RLHF —
  AlpacaEval2 gains must be weighed against that.
- **Only two output schemas.** Only binary and 6-class multiclass are
  explored; pointwise-scalar GenRMs are out of scope even though the
  framework could likely handle them.
- **HH-RLHF is "easy".** The authors themselves concede HH-RLHF prompts
  are easy, which is why CoT-GenRM and Think-RM policies show
  comparable LC WR; harder RLHF datasets would be more discriminative.

## Open Questions
- Does horizontal scaling also help **pointwise** GenRMs (non-pairwise tasks)?
- Can the long internal reasoning trace serve as a **process reward**
  signal (per-step credit) rather than just a final-label rationale?
- What is the **test-time compute vs quality** Pareto frontier compared
  to vertical scaling as we enlarge the backbone past 8B?
- Horizontal + vertical **combined** (long CoTs *and* vote over $k$ of
  them) — presumably best of both, but unreported.
- Transfer to **non-verifiable tasks** (creative writing, multi-criteria
  helpfulness) — the accuracy-only RL reward implicitly assumes a
  ground-truth label.

## Connections
- **Builds on:** [[generative-reward-model]], [[reinforcement-learning-from-human-feedback]], [[chain-of-thought]], [[group-relative-policy-optimization]]
- **Motivated by:** [[reward-hacking]] (a known failure mode of BT RMs)
- **Compared with:** Bradley-Terry reward modeling, CoT-GenRMs with
  vertical inference-time scaling (majority voting over $m{=}16$)
- **Extended by:** *(to be filled as wiki grows)*
- **Related concepts:** [[chain-of-thought]], [[group-relative-policy-optimization]]
- **Cites as prior RLHF foundations:**
  InstructGPT (Ouyang 2022), Deep RL from Human Preferences
  (Christiano 2017), DPO (Rafailov 2023), DeepSeek-R1 (Guo 2025, for GRPO)
- **Same-author other work in wiki:** *(none yet)*

## Personal Notes
- The core bet is that **a single long, self-reflective trace** is a
  better reward signal than **many short traces averaged**. Intuitively
  this mirrors the o1 / R1 insight at the reward-modeling layer rather
  than the policy layer — i.e. *the judge should also think long*.
- The pairwise-advantage derivation is the most transferable piece:
  it's a clean, 5-line proof that pairwise GenRMs slot into GRPO
  with no loss of mathematical fidelity. I'd like to see this
  generalized to PPO/GAE and to DPO-style offline training.
- Warm-up selection = *longest correct* is a reusable trick — an
  implicit form of *rejection sampling by length*, not just by
  correctness.
- Open concern: if RM-Bench itself has length bias, a 1k-token judge
  model could be exploiting that. The length-controlled AlpacaEval2
  numbers partially defuse this concern on the *policy* side but not
  on the RM-evaluation side.

## BibTeX
```bibtex
@article{hong2025thinkrm,
  title  = {Think-RM: Enabling Long-Horizon Reasoning in Generative Reward Models},
  author = {Hong, Ilgee and Yu, Changlong and Qiu, Liang and Yan, Weixiang and Xu, Zhenghao and Jiang, Haoming and Zhang, Qingru and Lu, Qin and Liu, Xin and Zhang, Chao and Zhao, Tuo},
  journal= {arXiv preprint arXiv:2505.16265},
  year   = {2025},
  url    = {https://arxiv.org/abs/2505.16265}
}
```
