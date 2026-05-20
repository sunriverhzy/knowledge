---
title: "What Makes a Reward Model a Good Teacher? An Optimization Perspective"
slug: "razin-2025-good-teacher"
authors:
  - "Noam Razin"
  - "Zixuan Wang"
  - "Hubert Strauss"
  - "Stanley Wei"
  - "Jason D. Lee"
  - "Sanjeev Arora"
year: 2025
venue: "NeurIPS 2025"
arxiv: "2503.15477"
domain: reward-modeling
tags: [reward-modeling, rlhf, alignment, theory, reward-variance, ppo, grpo, policy-optimization]
status: read
ingested: 2026-04-27
---

# What Makes a Reward Model a Good Teacher? An Optimization Perspective

## TL;DR
> Reward-model **accuracy** is not enough. A perfectly-accurate RM can
> flatten the [[reinforcement-learning-from-human-feedback]] landscape
> if it induces **low reward variance** on the initial policy —
> causing policy gradient to crawl. A less accurate RM with higher
> reward variance can be a **better teacher**. RM quality is therefore
> **policy-dependent** and cannot be captured by policy-agnostic
> benchmarks alone.

## Problem Statement
RLHF's success hinges on the reward model, but RM quality is measured
almost exclusively via **accuracy on preference benchmarks** (e.g.,
RewardBench, RM-Bench). Recent empirical work observed that *more
accurate RMs don't always yield better-aligned policies* — but no
theoretical explanation existed. Questions the paper pins down:

1. What *beyond accuracy* determines whether RLHF optimization makes
   progress?
2. Can a perfectly accurate RM still be a *bad teacher*?
3. Can the same RM be good for one base LLM and bad for another?

## Key Contributions
1. **Identifies `reward variance` as the missing factor.** Formally
   defines $\mathrm{Var}_{y\sim\pi_\theta(\cdot|x)}[r_\text{RM}(x,y)]$ —
   how much the RM separates outputs that the policy actually samples.
2. **Theorem 1** — Low reward variance ⇒ flat RLHF objective ⇒
   provably slow reward maximization, for general autoregressive
   policies (not just tabular). The time to increase expected reward
   by $\gamma$ is $\Omega(\text{Var}^{-1/3})$.
3. **Theorem 2** — A *perfectly accurate* RM can be **arbitrarily
   worse** than an *almost-completely-inaccurate* RM, purely because
   of reward variance.
4. **Theorem 3** — The same RM can be the best teacher for policy A
   and the worst teacher for policy B. **RM quality is joint with
   policy.**
5. **Experiments up to 8B** (Pythia, Llama-3.2; RLOO + GRPO; AlpacaFarm
   + UltraFeedback) confirm theory: reward variance correlates
   r = 0.98 with reward-maximization rate; accuracy alone correlates
   negatively in some regimes.
6. **Striking empirical finding**: even when the ground-truth reward
   is accessible, *using a proxy RM can outperform training against
   ground truth directly* in the first few epochs, because the proxy
   induces higher reward variance.

## Method Overview

This is a **theoretical + empirical** paper, not an algorithm paper.
The core conceptual moves:

### Definitions

**Accuracy** (Def. 1):
$$
\mathrm{acc}_{x,\mathcal D}(r_\text{RM})
= \mathbb E_{\{y,y'\}\sim\mathcal D}\!\left[\mathbf 1\!\left[\text{sign}(r_\text{RM}(x,y)-r_\text{RM}(x,y'))=\text{sign}(r_G(x,y)-r_G(x,y'))\right]\right].
$$
Depends only on **ranking**, not on the *degree* of separation.

**Reward variance** (Def. 2):
$$
\mathrm{Var}_{y\sim\pi_\theta(\cdot|x)}[r_\text{RM}(x,y)]
= \tfrac12 \mathbb E_{y,y'\sim\pi_\theta(\cdot|x)}\!\left[(r_\text{RM}(x,y) - r_\text{RM}(x,y'))^2\right].
$$
Depends on **how much** outputs are separated, and on **which outputs
the policy actually emits** — so it is inherently *policy-dependent*.

### Core Theorem (Theorem 1, abridged)

Let $\phi_\text{RLHF}(\theta) = \mathbb E_x \big[\mathbb E_{y\sim\pi_\theta(\cdot|x)}[r_\text{RM}(x,y)] - \lambda\, \mathrm{KL}(\pi_\theta \Vert \pi_\text{ref})\big]$.
Under gradient flow, the time for any reward $r$ (including $r_G$) to
increase by $\gamma$ is

$$
t_\gamma = \Omega\!\left(\Big(\mathbb E_{x\sim S}\!\big[\mathrm{Var}_{y\sim\pi_{\theta(0)}}[r_\text{RM}(x,y)]\big]\Big)^{-\tfrac13}\right),
$$

where $\Omega(\cdot)$ hides dependence on $\gamma, \lambda, L$, and the
Jacobian of the policy network. **Proof idea:** low reward variance
kills not only $\lVert\nabla\phi_\text{RLHF}\rVert$ but also all
higher-order derivatives, so the gradient norm can't grow quickly
during training either — yielding a tight lower bound on $t_\gamma$.

### Two Accuracy-vs-Variance Separations (Theorems 2 & 3)

Constructive: explicit tabular-policy constructions where a perfectly
accurate $r_\text{RM}$ is arbitrarily slow while an
almost-inaccurate $r'_\text{RM}$ achieves $t_\gamma = O(1)$. For
Theorem 3, two policy families $\Pi, \Pi'$ with matched expected
$r_G$ are constructed such that $r_\text{RM}$ teaches $\Pi$ well
and $\Pi'$ poorly, with $r'_\text{RM}$ the mirror image.

## Experiments & Results

**Setup.** Ground-truth reward simulated by ArmoRM-8B. Policy base
Pythia-2.8B (+ Pythia-1B, Llama-3.2-1B/3B ablations). UltraFeedback
prompts, 80/20 split for RM train vs RL. RLOO default, GRPO in ablations.

**RMs differ in reward variance** by varying the % of on-policy
preference pairs during RM training (100 %, 75 %, 50 %, 25 %, 0 %),
plus a synthetic "perfectly-accurate, low-variance" control.

### RM properties (Pythia-2.8B init; rewards normalized)

| RM | 100% on-policy | 75% | 50% | 25% | 0% | Perfect-Acc/Low-Var | Ground Truth |
|----|----------------|-----|-----|-----|----|----|----|
| Reward variance | **0.630** | 0.616 | 0.555 | 0.438 | 0.314 | 0.111 | 0.256 |
| On-policy acc. | 0.660 | 0.656 | 0.640 | 0.616 | 0.587 | **1.000** | **1.000** |
| Off-policy acc. | 0.630 | 0.732 | 0.758 | 0.754 | 0.762 | **1.000** | **1.000** |

### Correlation with reward-increase after 1 epoch (Table 2)

| Measure | Pearson (proxy ↑) | Pearson (GT ↑) |
|---------|-------------------|----------------|
| Reward variance | **0.982** | **0.834** |
| On-policy acc. | −0.762 | −0.283 |
| Off-policy acc. | −0.933 | −0.507 |
| Reward variance × Acc. | 0.676 | **0.940** |

### Key qualitative findings

- The perfectly-accurate, low-variance RM *underperformed* all five
  noisier-but-higher-variance RMs on ground-truth reward increase.
- Proxy RMs **beat the ground-truth reward** during early epochs when
  they induced higher reward variance on-policy.
- Across 3 base models × 4 public RMs, the **best RM differs per base
  model** (Theorem 3 confirmed empirically).

## Strengths

- **Theoretically rigorous, empirically grounded.** Rare combination
  for alignment work: formal $\Omega$/$O$ bounds + up-to-8B experiments
  that match the predictions quantitatively (Pearson > 0.98).
- **Actionable diagnostic.** Reward variance is cheap to measure from
  the initial policy — this gives practitioners a *pre-RL* screen for
  whether an RM will be a useful teacher, complementing RewardBench.
- **Reframes RM evaluation.** The "RM-as-teacher, not RM-as-classifier"
  framing is a clean conceptual move that generalizes to knowledge
  distillation and other teacher-student settings.
- **Extends to verifiable rewards.** Analysis is reward-source-agnostic,
  so it straightforwardly applies to rule-based RL (e.g., GRPO with
  math/code rewards) — an increasingly important regime.
- **Identifies a concrete mitigation axis.** If low variance is the
  problem, *margin-based RM objectives* and on-policy preference data
  become natural remedies.

## Limitations & Weaknesses

- **Tabular-policy assumption in Thms 2 & 3.** The most striking
  separations (perfect-acc RM arbitrarily worse than near-random RM)
  hold for tabular policies; only Theorem 1 generalizes to neural
  autoregressive policies. Closing this gap is open.
- **Gradient flow ≠ practical SGD.** Continuous-time analysis at the
  small-learning-rate limit. Stochastic-gradient, mini-batch, clipping
  (PPO/GRPO) dynamics are not directly covered.
- **Ground truth simulated by another RM (ArmoRM).** If ArmoRM has its
  own biases, the "reward variance beats accuracy" finding could
  partially inherit them. Authors flag this; human-eval replication
  would strengthen the story.
- **Scale caps at 8B.** Pythia-2.8B + Llama-3.2 ≤ 3B base policies;
  RMs up to 8B. Scaling to frontier-size models may change the
  variance / accuracy Pareto.
- **No constructive fix.** The paper shows low variance is lethal but
  does *not* propose a concrete RM training objective that raises
  variance without degrading accuracy. Reward scaling alone
  demonstrably fails.
- **Best-of-N side-remark, not full treatment.** The asymmetry between
  RLHF (accuracy not enough) and Best-of-N (accuracy optimal) is noted
  but under-explored.

## Open Questions
- How should we **redesign RM training** to *jointly* maximize
  accuracy and reward variance (margin-based losses, on-policy data,
  ensembling)?
- How does **reward variance behave during training**, not just at
  $t=0$? Does it collapse as the policy drifts off-manifold?
- Can **[[generative-reward-model]]s** (e.g., [[hong-2025-think-rm]])
  be analyzed in this framework? Their long-CoT outputs presumably
  give different variance profiles than scalar BT RMs.
- What is the variance / accuracy trade-off under **[[group-relative-policy-optimization]]**
  specifically — does GRPO's group-mean baseline change the picture?
- Does reward variance predict **end-policy quality** after many
  epochs, or only the short-horizon optimization rate?
- **Best-of-N vs RLHF**: the paper shows accuracy is sufficient for
  Best-of-N but not RLHF. What about DPO, rejection sampling,
  constitutional alignment?

## Connections
- **Builds on:** [[reinforcement-learning-from-human-feedback]]
  (pipeline it analyzes); Razin et al. 2024 (vanishing-gradient /
  reward-variance connection it invokes for Thm 1).
- **Motivated by:** [[reward-hacking]] — proposes reward variance as
  an *additional* knob orthogonal to the accuracy-hacking trade-off.
- **Closely related to (and contrasted with):** [[hong-2025-think-rm]]
  — both ask "what makes an RM good?" but from complementary angles:
  Think-RM improves the RM's *judgment quality*; this paper argues
  that even a perfect-judgment RM can fail as a teacher. See the
  [[think-rm-vs-good-teacher]] comparison page.
- **Related concepts:** [[group-relative-policy-optimization]] (used
  in Appendix D.2), PPO, RLOO, reward shaping.
- **Extended by:** *(to be filled as wiki grows)*
- **Same-author other work in wiki:** *(none yet)*

## Personal Notes
- The paper's single most quotable sentence — "*regardless of how
  accurate a reward model is, if it induces low reward variance, then
  the RLHF objective suffers from a flat landscape*" — feels like a
  Goodhart's Law inversion: maximizing a proxy (accuracy) with no
  variance component optimizes the *wrong* moment of the
  reward-preference distribution.
- The result pairs beautifully with [[hong-2025-think-rm]]: that paper
  *raises* RM accuracy and judgment depth; this paper says *accuracy
  without variance is insufficient*. Natural synthesis: a long-CoT
  GenRM that is *also* trained with a margin-loss to raise variance
  on the target policy. I'd bet this combination is SOTA.
- A practical recipe falls out: before running RLHF, sample $G \ge 8$
  rollouts per prompt from your SFT policy, score them with your
  candidate RMs, compute empirical reward variance, and drop any RM
  below a variance threshold — no matter how good its RewardBench
  score. Basically a "variance screen" on top of accuracy benchmarks.
- The Best-of-N counterexample (where accuracy *is* sufficient) is a
  gift — it suggests a general principle: **the alignment method's
  dependence on the RM's *distributional* behavior vs. its *ranking*
  behavior determines whether variance matters**. This dichotomy
  deserves its own framework.

## BibTeX
```bibtex
@inproceedings{razin2025goodteacher,
  title     = {What Makes a Reward Model a Good Teacher? An Optimization Perspective},
  author    = {Razin, Noam and Wang, Zixuan and Strauss, Hubert and Wei, Stanley and Lee, Jason D. and Arora, Sanjeev},
  booktitle = {Advances in Neural Information Processing Systems (NeurIPS)},
  year      = {2025},
  url       = {https://arxiv.org/abs/2503.15477}
}
```
