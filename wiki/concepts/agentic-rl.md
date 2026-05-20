---
title: "Agentic RL"
aliases: ["Agentic Reinforcement Learning", "RL for Agents", "Agent RL", "Agentic RL for GUI"]
domain: agent
tags: [agent, rl, gui-agent, computer-use, rlvr, grpo, multi-turn, online-rl, agentic-rl]
source_count: 24
last_updated: 2026-05-20
---

# Agentic RL

## Definition
**Agentic RL** is reinforcement learning applied to train **autonomous agents that interact with real or simulated environments** over extended horizons to complete complex tasks. It is distinguished from standard RLHF / text-generation RL by five structural properties:

1. **Environment interaction** — the agent acts in a real world (GUI, OS, browser, mobile), not on static text preference pairs.
2. **Trajectory-level optimization** — optimization over 20–200+ step trajectories, not single responses.
3. **Verifiable environment rewards (RLVR)** — rewards derived from objective state changes (URL, DB, file, DOM), not learned reward models.
4. **Long-horizon credit assignment** — success signal at step 200 must propagate back to actions at step 5.
5. **Infrastructure as algorithm** — parallel simulators, async rollout, world models, KV compression are first-order design choices, not implementation details.

$$
\text{Agentic RL} = \text{RLVR} + \text{Multi-turn optimization} + \text{Environment interaction} + \text{Scalable infrastructure}
$$

## Intuition
Standard RLHF asks: "Given this prompt, which response does the human prefer?" Agentic RL asks: "Given this 200-step computer task, can the agent learn to complete it from scratch through trial and error in a real environment?" The agent must perceive, plan, act, verify, recover from errors, and improve — all through RL signal from the environment itself.

## Historical Context

| Period | Milestone |
|--------|-----------|
| 2017 | AlphaGo/Zero: RL with sparse terminal reward + self-play |
| 2022 | RLHF (InstructGPT): RL for text generation alignment |
| 2024 | DigiRL: first offline-to-online RL for device-control agents |
| 2025 Q1 | DeepSeek-R1 / GUI-R1 / UI-R1: GRPO + RLVR → emergent reasoning |
| 2025 Q2–Q4 | WebRL, MobileRL, ComputerRL, Hi-Agent: online / hierarchical / hybrid RL for GUI |
| 2026 | Mobile-Agent-v3.5 (MRPO), Continual GUI Agents, ACuRL, VAGEN: multi-platform RL, continual RL, agentic verification |

## The Agentic RL Training Pipeline

```
┌─────────────────────────────────────────────────────────────────────┐
│  Stage 0: VLM Backbone Pretraining (Qwen-VL / InternVL / ...)      │
├─────────────────────────────────────────────────────────────────────┤
│  Stage 1: SFT on Demonstration Data                                │
│  ├── Human/synthetic trajectories (AitW, Mind2Web, UI-TARS data)   │
│  ├── Reasoning augmentation: (State, Thought, Action) triplets     │
│  └── Goal: learn basic UI semantics, "safe initialization"         │
├─────────────────────────────────────────────────────────────────────┤
│  Stage 2: Offline RL / Preference Learning                         │
│  ├── DPO on success/failure trajectory pairs                       │
│  ├── Or: Offline GRPO with verifiable rewards                      │
│  └── Goal: learn "which trajectory is better" from static data     │
├─────────────────────────────────────────────────────────────────────┤
│  Stage 3: Online / Semi-online RL                                  │
│  ├── Agent interacts with real envs (AndroidWorld/WebArena/OSWorld) │
│  │   or world models (DynaWeb/ViMo/Code2World)                     │
│  ├── GRPO / M-GRPO + verifiable environment rewards                │
│  ├── Curriculum learning: failure-set → simplified variants         │
│  └── Goal: learn recovery, adaptation, long-horizon planning       │
├─────────────────────────────────────────────────────────────────────┤
│  Stage 4: Continual RL / Self-improvement (frontier direction)     │
│  ├── Continual GUI Agents (GUI-AiF): adapt without forgetting      │
│  ├── ACuRL: autonomous curriculum, zero human data                 │
│  └── Goal: agent evolves as apps evolve                            │
└─────────────────────────────────────────────────────────────────────┘
```

## Data Construction Paradigms

| Paradigm | Source | Example |
|----------|--------|---------|
| **Human demonstrations** | Real user interactions recorded as trajectories | AitW (715K), Mind2Web, UI-TARS data |
| **MCTS / Search synthesis** | Guided tree search discovers high-quality trajectories | Agent Q, M2-Miner |
| **RLVR online sampling** | Agent interacts with env, gets verifiable reward | DigiRL, WebRL, MobileRL, ComputerRL |
| **World model dreaming** | Predict next state without real env; 1000× throughput | DynaWeb, WebWorld, Code2World, ViMo |
| **Continual self-generation** | Agent explores new envs → evaluates → generates curriculum → trains | ACuRL, GUI-AiF |

## RL Algorithm Variants for Agentic RL

| Algorithm | Key adaptation for agents |
|-----------|--------------------------|
| **GRPO** | Critic-free; group-normalized advantage; memory-efficient for 7B–72B VLMs |
| **M-GRPO** | Whole trajectory as optimization unit (not single step) |
| **AdaGRPO** | Difficulty-weighted: low-success tasks get higher gradient |
| **AEPO** | Adaptive exploration in continuous coordinate spaces |
| **GuAE** | Anchor-based tempering against advantage collapse |
| **MRPO** | Multi-platform RL preventing cross-platform conflicts |
| **Hierarchical GRPO** | Planner + Executor dual-level optimization |
| **DPO** | Offline preference pairs from successful vs failed trajectories |
| **IQL/CQL + Best-of-N** | Value-based offline RL with inference-time action reranking |
| **MCTS + DPO** | Search generates high-quality preference data |

## Reward Architecture (Three-Tier Pyramid)

```
┌─────────────────────────────────────────┐
│  Tier 3: LLM-as-Judge / Learned RM     │  ← General but hackable
├─────────────────────────────────────────┤
│  Tier 2: Process Rewards (ProgRM/PRM)  │  ← Dense but needs milestone extraction
├─────────────────────────────────────────┤
│  Tier 1: Rule-based Verifiable         │  ← Noise-free but sparse
│  (URL match, bbox IoU, DB check)       │
└─────────────────────────────────────────┘
```

SOTA systems use **composite rewards** combining all three tiers.

## Infrastructure Requirements

| Challenge | Solution | Example |
|-----------|----------|---------|
| GUI I/O latency (0.5–2s/step) | Parallel envs × 512 | MAI-UI |
| GPU idle during env wait | Async actor-trainer separation | AReaL |
| Trajectory > context window | Dynamic context compression | WebAgent-R1 |
| Env non-reproducible | World model dream rollouts | DynaWeb |
| KV cache explosion | Spatio-temporal KV compression | GUI-KV |
| Unsafe real-env exploration | Offline → Semi-online → Online | DigiRL pipeline |

## Key Papers

| Paper | Year | Core Agentic RL contribution |
|-------|------|------------------------------|
| [[bai-2024-digiirl]] | 2024 | Canonical offline-to-online RL; VLM-as-evaluator |
| [[luo-2025-gui-r1]] | 2025 | RLVR + GRPO; 3K samples → emergent reasoning |
| [[qi-2024-webrl]] | 2024 | Self-evolving curriculum + ORM for web agents |
| [[wei-2025-webagent-r1]] | 2025 | M-GRPO: trajectory-level multi-turn optimization |
| [[lai-2025-computerrl]] | 2025 | End-to-end online RL; API-GUI hybrid; Entropulse |
| [[wu-2025-hi-agent]] | 2025 | Hierarchical Planner+Executor GRPO; 87.9% AitW |
| [[xu-2025-mobilerl]] | 2025 | AdaGRPO; difficulty-adaptive mobile RL |
| [[ding-2026-dynaweb]] | 2026 | Web World Model; model-based RL for web agents |
| [[xu-2026-mobile-agent-v3-5]] | 2026 | MRPO; multi-platform RL; 20+ benchmark SOTA |
| [[liu-2026-continual-gui-agents]] | 2026 | First continual RL for GUI agents |
| [[guo-2026-acurl]] | 2026 | Autonomous curriculum RL; zero human data |
| [[cui-2026-agentic-reward-modeling]] | 2026 | VAGEN: active verifier agent for RLVR |
| [[hu-2026-faithful-mobile-gui]] | 2026 | GuAE: solves advantage collapse in sparse GUI RL |

## Comparison with Related Concepts

|                       | Agentic RL                    | [[reinforcement-learning-from-human-feedback]] | Supervised Agent Training (SFT/BC) |
|-----------------------|-------------------------------|------------------------------------------------|------------------------------------|
| Interaction           | Real environment              | Static preference pairs                        | None (offline demonstrations)      |
| Horizon               | 20–200+ steps                 | 1 response                                     | 1 response / fixed trajectory      |
| Reward source         | Environment verification      | Learned human preference model                 | N/A (teacher forcing)              |
| Credit assignment     | Hard (long-horizon)           | Easy (single response)                         | N/A                                |
| Infrastructure needed | Parallel envs, world models   | Standard GPU training                          | Standard GPU training              |
| Failure mode          | Unsafe actions, env latency   | [[reward-hacking]]                             | Compounding error (covariate shift)|
| Can discover new strategies? | ✅ Yes             | Partially                                      | ❌ No (bounded by demonstrations)  |

## Current Research Frontiers
- **Continual Agentic RL**: agents that keep improving as their target environments evolve.
- **Process rewards for long-horizon agent tasks**: dense credit without manual annotation.
- **World-model fidelity**: can dream rollouts replace real-env interaction for complex OS tasks?
- **Safe exploration in irreversible environments**: prevent catastrophic actions during online RL.
- **Multi-platform generalization**: one RL-trained agent across web, mobile, and desktop.
- **Agentic verification (VAGEN)**: using RL-trained verifier agents to produce rewards for other agents.

## See Also
- [[gui-agent]]
- [[computer-use-agent]]
- [[reinforcement-learning-with-verifiable-rewards]]
- [[group-relative-policy-optimization]]
- [[multi-turn-gui-rl]]
- [[gui-io-wall]]
- [[gui-world-model]]
- [[api-gui-hybrid-action-space]]
- [[gui-grounding]]
