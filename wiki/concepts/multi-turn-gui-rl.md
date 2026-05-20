---
title: "Multi-Turn GUI RL"
aliases: ["Multi-turn GUI Reinforcement Learning", "M-GRPO", "Long-horizon GUI RL"]
domain: agent
tags: [agent, gui-agent, rl, grpo, multi-turn, long-horizon]
source_count: 8
last_updated: 2026-05-20
---

# Multi-Turn GUI RL

## Definition
**Multi-Turn GUI RL** refers to reinforcement learning methods that optimize **entire interaction trajectories** for GUI / computer-use tasks, rather than individual actions. A GUI task trajectory $\tau = (o_0, a_0, o_1, a_1, \ldots, o_T, a_T, r_T)$ consists of 20–200+ steps; standard single-step RL algorithms face severe credit-assignment challenges under such long horizons.

Multi-turn methods treat the trajectory as the optimization unit, using techniques like Multi-Turn GRPO (M-GRPO), hierarchical advantage estimation, or dynamic context compression to handle long-horizon credit assignment.

## Intuition
Giving a GUI agent a reward only at step 200 (when the form is submitted) is like training a chess player who only learns they won or lost at the end of the game, with no intermediate feedback. Multi-turn RL provides structure: either decompose the reward into intermediate credits, or optimize the whole trajectory as a unit.

## Key Variants / Subtypes

### Multi-Turn GRPO (M-GRPO)
Proposed by [[wei-2025-webagent-r1]]: treat the full trajectory $\tau$ as the optimization sample, analogous to how standard GRPO treats a single response.

$$
\hat A(\tau_i) = \frac{r(\tau_i) - \bar r}{\hat\sigma_r}, \quad \text{update: } \max_\theta \sum_i \hat A(\tau_i) \sum_t \log \pi_\theta(a_t | o_t, H_t)
$$

This allows learning "delayed gratification" — early actions that set up success 50 steps later.

### Hierarchical RL
Separate **Planner** (generates semantic subgoals) and **Executor** (performs atomic UI actions). Hi-Agent (`2510.14388`) achieves 87.9% on AitW with Planner+Executor jointly trained via GRPO and a Foresight Advantage Function.

### Credit assignment strategies

| Strategy | Mechanism | Example |
|----------|-----------|---------|
| **Sparse terminal reward** | $r_T = 1$ only on task completion | Standard WebArena, OSWorld |
| **Process reward** | Reward per verified subgoal | ProgRM, AgentPRM |
| **HIPER credit assignment** | Hierarchical RL with structured reward decomposition | HIPER (`2602.16165`) |
| **M-GRPO trajectory level** | Whole-trajectory optimization | WebAgent-R1 |

### System 1 / System 2 dual-mode routing
A recurring finding: reasoning (CoT) helps planning but hurts precise grounding. Multi-turn systems increasingly learn *when* to invoke each mode:

- **System 1** (fast): direct coordinate prediction without chain-of-thought.
- **System 2** (slow): reasoning / planning for sub-task decomposition, error recovery, and strategy.

[[liu-2026-infigui-g1]] demonstrates this empirically; the routing function itself is a target for RL optimization.

### Historical context issue (HGPO)
When GUI agents are trained step-by-step with GRPO over long trajectories, optimizing each step against a stale history can assign credit to actions the agent didn't actually take in that state (because prompts interleave screenshots, summaries, tool traces). HGPO (He et al., 2026) addresses historical-context inconsistency in stepwise GRPO updates.

## Key Papers

| Paper | Year | Contribution |
|-------|------|--------------|
| [[wei-2025-webagent-r1]] | 2025 | M-GRPO: trajectory-level multi-turn RL for web agents. |
| [[wu-2025-hi-agent]] | 2025 | Hierarchical VLA: Planner+Executor with GRPO; 87.9% AitW. |
| [[lu-2025-ui-s1]] | 2025 | Semi-online trajectory patching: simulates multi-turn correction offline. |
| [[bai-2024-digiirl]] | 2024 | Offline-to-online RL; instruction-level value functions for long-horizon tasks. |
| [[peng-2026-hiper]] | 2026 | HIPER: hierarchical RL with structured reward decomposition. |

## Comparison with Related Concepts

|                       | Multi-Turn GUI RL             | Single-step RL                |
|-----------------------|-------------------------------|-------------------------------|
| Optimization unit     | Full trajectory / subgoal     | Single action token           |
| Credit assignment     | Hard (delay = 20–200 steps)   | Easy (delay = 1 step)         |
| Typical horizon       | 20–200 steps                  | 1 step                        |
| Required infrastructure | Trajectory rollout, memory management | Standard GRPO / PPO  |

## Current Research Frontiers
- **Process rewards for long-horizon GUI tasks**: rewarding subgoal completion rather than only terminal success.
- **Memory compression**: how to maintain task-relevant context across 200+ step trajectories without context overflow.
- **System 1 / System 2 learned routing**: when to plan, when to act, when to verify.
- **Safe long-horizon exploration**: how to avoid irreversible errors deep into a 200-step trajectory.

## See Also
- [[gui-agent]]
- [[reinforcement-learning-with-verifiable-rewards]]
- [[group-relative-policy-optimization]]
- [[gui-io-wall]]
- [[gui-world-model]]
