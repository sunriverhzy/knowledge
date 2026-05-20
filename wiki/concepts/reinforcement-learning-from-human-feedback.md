---
title: "Reinforcement Learning from Human Feedback"
aliases: ["RLHF"]
domain: llm
tags: [rlhf, alignment, rl, llm, reward-modeling]
source_count: 2
last_updated: 2026-04-27
---

# Reinforcement Learning from Human Feedback

## Definition
**Reinforcement Learning from Human Feedback (RLHF)** is a post-training
paradigm for large language models in which a policy $P_\phi$ is
optimized to maximize a *learned* reward signal $r$ whose optimum
reflects human preferences:

$$
\max_\phi \;\mathbb E_{x\sim\mathcal D,\; y\sim P_\phi(\cdot\mid x)}\big[ r(x,y) \big]
\;-\; \beta\, D_{\text{KL}}(P_\phi \Vert P_{\phi_\text{ref}}).
$$

The reward $r$ is typically produced by a **reward model** (RM) trained
on human preference comparisons between response pairs.

## Intuition
Pretraining optimizes likelihood on raw text — not usefulness or
safety. RLHF closes that gap by **letting humans (or their learned
proxies) grade model outputs** and turning those grades into a
differentiable objective the model can follow. A KL penalty to the
pretraining reference keeps the model from collapsing to a narrow,
reward-hacking mode.

## Historical Context
- **2017** — Christiano et al. introduce *Deep RL from Human Preferences*,
  establishing the preference-learning + PPO recipe on simulated robotic
  tasks.
- **2020** — Stiennon et al. apply RLHF to **summarization** with GPT-3,
  producing the first LLM-scale demonstration.
- **2022** — Ouyang et al., *InstructGPT*, scales RLHF to general
  instruction following — the template adopted by ChatGPT and every
  major aligned LLM since.
- **2023–2024** — Wave of alternatives and refinements: **DPO**
  (Rafailov 2023) removes the explicit reward model by reparameterizing
  the preference objective; **RLAIF** replaces human labels with AI
  labels; **Constitutional AI** uses principles-based self-critique.
- **2024–2025** — Reward modeling itself becomes a research subject:
  [[reward-hacking]] diagnosis, [[generative-reward-model]]s,
  process reward models, and — for reasoning tasks — rule-based
  verifiable rewards (e.g. DeepSeek-R1 with GRPO).

## Key Variants / Subtypes

- **Classical RLHF (PPO + BT RM)** — Ouyang 2022. BT reward model +
  Proximal Policy Optimization with KL penalty to reference. The default.
- **DPO (Direct Preference Optimization)** — Rafailov 2023. Eliminates
  the RL loop; the LLM *is* the (implicit) reward model.
- **GRPO (Group Relative Policy Optimization)** — Shao/Guo et al.
  (DeepSeek). Value-function-free advantage estimation by group
  normalization; popular in reasoning RL. See [[group-relative-policy-optimization]].
- **RLAIF / Constitutional AI** — substitute human labels with an LLM
  judge (Bai et al.).
- **Pairwise RLHF (with GenRMs)** — [[hong-2025-think-rm]]. Keep RLHF
  pairwise end-to-end, no pointwise reward conversion.
- **Rule-based / verifiable RL** — when a ground-truth answer exists
  (math, code), use it directly as reward (DeepSeek-R1 style). Often
  labeled "RL from verifiable reward" rather than RLHF.

## Technical Details

### Standard PPO-RLHF pipeline
1. **SFT** on high-quality demonstrations.
2. **RM training** — fit a Bradley–Terry model on human preference
   pairs $(y_w \succ y_l \mid x)$ by minimizing
   $-\mathbb E[\log \sigma(r(x,y_w) - r(x,y_l))]$.
3. **PPO optimization** of the policy against $r$ with a per-token KL
   penalty toward $\phi_\text{ref}$.

### PPO surrogate (used in RLHF)
$$
L_\text{CLIP}(\phi) = \mathbb E_t\!\left[ \min\!\left(\rho_t \hat A_t,\;
\text{clip}(\rho_t, 1{-}\epsilon, 1{+}\epsilon) \hat A_t\right)\right]
\;-\; \beta\, D_\text{KL}(P_\phi \Vert P_{\phi_\text{ref}}).
$$

### Advantage estimation
Either [GAE](https://arxiv.org/abs/1506.02438) (requires value function)
or [[group-relative-policy-optimization]] (no value function, group-normalized
rewards).

## Key Papers

| Paper | Year | Contribution |
|-------|------|--------------|
| [[hong-2025-think-rm]] | 2025 | Pairwise RLHF pipeline for GenRMs — skips pointwise reward conversion entirely. |
| [[razin-2025-good-teacher]] | 2025 | Theoretical analysis of the RLHF objective: low reward variance ⇒ flat landscape ⇒ slow reward maximization, *regardless of RM accuracy*. RM quality is policy-dependent. |

*(Foundational papers to ingest: Christiano 2017; Stiennon 2020; Ouyang
2022 — InstructGPT; Rafailov 2023 — DPO; Guo 2025 — DeepSeek-R1.)*

## Comparison with Related Concepts

|                | RLHF (PPO + BT RM)  | DPO                   | Rule-based RL (verifiable reward) |
|----------------|---------------------|-----------------------|-----------------------------------|
| Needs RM?      | Yes                 | No (implicit)         | No (needs verifier / rule)        |
| Online or offline? | Online           | Offline               | Online                            |
| Reward source  | Learned RM          | Preference pairs      | Ground-truth verifier             |
| Failure mode   | [[reward-hacking]]  | Distribution collapse | Specification gaming              |
| Best for       | Open-ended tasks    | When preference data is abundant | Math / code / verifiable tasks |

## Current Research Frontiers
- **Better reward models** — [[generative-reward-model]]s, process
  rewards, long-horizon reasoning RMs.
- **Reward-model-as-teacher framing** — see [[reward-model-as-teacher]]
  and [[reward-variance]]; accuracy benchmarks under-specify RM
  quality, and RM evaluation should be policy-conditional
  ([[razin-2025-good-teacher]]).
- **RLHF without humans** — RLAIF, constitutional AI.
- **Non-verifiable tasks** — how to apply rule-based RL ideas
  (GRPO-style) outside of math/code.
- **Multi-reward aggregation** — mechanism design for fine-tuning with
  multiple RMs.
- **Long-horizon / agent RLHF** — trajectory-level rewards for
  multi-turn agents, not just single-response rewards.

## See Also
- [[generative-reward-model]]
- [[group-relative-policy-optimization]]
- [[reward-hacking]]
- [[reward-variance]]
- [[reward-model-as-teacher]]
- [[chain-of-thought]]
