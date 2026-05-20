---
title: "Reinforcement Learning with Verifiable Rewards"
aliases: ["RLVR", "RL with Verifiable Rewards", "Verifiable Reward RL"]
domain: agent
tags: [rl, rlvr, agent, gui-agent, grpo, reward-modeling, alignment]
source_count: 12
last_updated: 2026-05-20
---

# Reinforcement Learning with Verifiable Rewards

## Definition
**Reinforcement Learning with Verifiable Rewards (RLVR)** is an RL training paradigm in which reward signals are derived from **objective, environment-verifiable facts** — such as whether a URL changed, whether a database row was updated, whether a bounding-box was hit — rather than from a learned reward model or human preference labels. The verifier is a deterministic function of environment state, not a neural network.

$$
r(s_t, a_t) = \text{verify}(\text{env\_state}_{t+1}, \text{goal})
\in \{0, 1\} \text{ or } [0,1]
$$

RLVR is distinct from RLHF in that rewards cannot be hacked by exploiting the weaknesses of a learned reward model — the environment state is the ground truth.

## Intuition
In GUI / computer-use tasks, the environment provides **uncheat-able feedback**: either the purchase went through or it didn't; either the file was created or it wasn't. This deterministic verifiability transforms GUI automation into the ideal testbed for RLVR — analogous to how math / code environments enable verifiable RL for LLM reasoning (DeepSeek-R1 style).

The key insight: **GUI tasks are "RLVR tasks in disguise"** — they just require visual perception and action execution rather than symbolic proof generation.

## Historical Context
- **DeepSeek-R1 (2025)** — demonstrated that GRPO + rule-based verifiable rewards on math/code tasks elicits emergent Chain-of-Thought reasoning.
- **GUI-R1**, **UI-R1 (2025)** — directly apply RLVR / GRPO to GUI grounding and multi-step tasks; emergent internal monologues.
- **DigiRL, WebRL, ComputerRL, AndroidWorld (2024–2025)** — use execution-based environment state as verifiable rewards.

## Key Variants / Subtypes

- **Binary RLVR**: $r = 1$ on success, $0$ on failure. Clean but sparse.
- **Partial credit RLVR** (GUI grounding): $r \in \{0, 0.5, 1\}$ for exact / direction / wrong.
- **Continuous RLVR** (grounding): $r = f(\text{dist, IoU, Gaussian})$.
- **Process-level RLVR**: intermediate milestones verified and rewarded. See ProgRM, AgentPRM.
- **Composite RLVR**: combine multiple verifiable signals (coordinate IoU + action type + format validity).

## Technical Details

### RLVR with GRPO

For GUI agents, [[group-relative-policy-optimization]] (GRPO) is the most common algorithm for RLVR:

1. For each task prompt, sample $G$ trajectories from the current policy.
2. Execute each in the environment; collect verifiable rewards $r_i$.
3. Compute normalized advantages: $\hat A_i = (r_i - \bar r) / \hat\sigma_r$.
4. Update policy: $\max_\theta \mathbb{E}\left[\sum_i \hat A_i \log \pi_\theta(a_i | o_i)\right]$ with KL penalty.

No learned critic is needed. Verifiable environment rewards replace the critic's value estimates.

### Why RLVR is better than RLHF for GUI tasks

| Dimension | RLHF (learned RM) | RLVR (environment verifier) |
|-----------|-------------------|------------------------------|
| Reward source | Learned model | Deterministic environment |
| Reward hacking | High risk | Low risk (verifier can't be fooled) |
| Data needed | Preference pairs | Executable task definitions |
| Scalability | Limited by RM quality | Scales with environment parallelism |
| Coverage | General | Verifiable tasks only |

### Limitations
RLVR only works when tasks are **verifiable**. For open-ended tasks (creative writing, subjective helpfulness), verification is impossible and learned reward models remain necessary.

## Key Papers

| Paper | Year | Contribution |
|-------|------|--------------|
| [[luo-2025-gui-r1]] | 2025 | GUI-R1: RLVR + GRPO on GUI tasks; 3K samples → SOTA; emergent reasoning. |
| [[lu-2025-ui-r1]] | 2025 | UI-R1: rule-based RLVR + DAST for GUI grounding and multi-step tasks. |
| [[bai-2024-digiirl]] | 2024 | DigiRL: verifiable device-control rewards from AndroidEnv; offline-to-online RL. |
| [[qi-2024-webrl]] | 2024 | WebRL: outcome-supervised RM + curriculum as RLVR for web agents. |
| [[lai-2025-computerrl]] | 2025 | ComputerRL: end-to-end RLVR for computer use with API-GUI hybrid actions. |

## Comparison with Related Concepts

|               | RLVR                        | [[reinforcement-learning-from-human-feedback]] | Rule-based rewards         |
|---------------|-----------------------------|------------------------------------------------|---------------------------|
| Reward source | Environment side-effect     | Learned human preference model                 | Hand-crafted heuristics   |
| Hackable?     | Very low                    | Yes ([[reward-hacking]])                       | Medium                    |
| Coverage      | Verifiable tasks only       | General                                        | Limited to predefined rules |
| Scalability   | High (env parallelism)      | Limited (preference data)                      | Medium                    |

## Current Research Frontiers
- **Verifiable composite rewards**: combining URL checks, DOM diffs, file-state inspection for long-horizon tasks.
- **Process-level RLVR**: verifying intermediate subgoals, not just final outcomes.
- **RLVR for non-verifiable tasks**: how to extend the RLVR paradigm to subjective tasks (writing quality, user preference).
- **Sim-to-real**: RLVR signals in simulators; bridging to real environments.

## See Also
- [[gui-agent]]
- [[group-relative-policy-optimization]]
- [[reward-hacking]]
- [[reinforcement-learning-from-human-feedback]]
- [[multi-turn-gui-rl]]
