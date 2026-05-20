---
title: "Reading List: Agentic RL for GUI Agents"
domain: [agent, rl]
difficulty: advanced
last_updated: 2026-05-20
---

# Reading List: Agentic RL for GUI Agents

## Purpose
For researchers who want to understand how RL is used to train GUI / computer-use agents — from offline initialization to online interaction to continual self-improvement. Covers algorithms, data construction, reward design, and infrastructure.

## Prerequisite Concepts
- [[gui-agent]]
- [[reinforcement-learning-with-verifiable-rewards]]
- [[group-relative-policy-optimization]]
- [[multi-turn-gui-rl]]

---

## Stage 1 — Foundations: Environments & Offline RL

*Understand what GUI RL optimizes against, and how to learn from static data.*

1. [[xie-2024-osworld]] — *The canonical real-computer benchmark; execution-based evaluation.*
2. [[rawles-2024-androidworld]] — *Dynamic mobile RL environment with verifiable rewards.*
3. [[zhou-2023-webarena]] — *Realistic web environment with executable verification.*
4. [[bai-2024-digiirl]] — *DigiRL: the canonical offline-to-online framework. Start here for understanding the training pipeline.*
5. [[bai-2025-digi-q]] — *Digi-Q: offline Q-learning; frozen backbone + Best-of-N at inference.*

## Stage 2 — Core Methods: GRPO / RLVR for GUI

*The dominant algorithm family for Agentic RL.*

6. [[luo-2025-gui-r1]] — *GUI-R1: RLVR + GRPO; 3K samples; emergent System-2 reasoning. The "hello world" of GUI Agentic RL.*
7. [[lu-2025-ui-r1]] — *UI-R1: rule-based RL + DAST (difficulty-adaptive slow-thinking).*
8. [[qi-2024-webrl]] — *WebRL: self-evolving curriculum + Outcome-supervised RM.*
9. [[wei-2025-webagent-r1]] — *M-GRPO: trajectory-level multi-turn optimization.*
10. [[xu-2025-mobilerl]] — *MobileRL: AdaGRPO + difficulty weighting for mobile.*

## Stage 3 — Scaling: Online RL, Hybrid, and Hierarchical

*Moving beyond offline: real-env interaction, hybrid strategies, hierarchical decomposition.*

11. [[lai-2025-computerrl]] — *ComputerRL: end-to-end online RL; API-GUI hybrid; Entropulse.*
12. [[wu-2025-hi-agent]] — *Hi-Agent: hierarchical Planner+Executor; 87.9% AitW.*
13. [[lu-2025-ui-s1]] — *UI-S1: semi-online RL via trajectory patching.*
14. [[shi-2025-mobilegui-rl]] — *MobileGUI-RL: scalable online GRPO with parallel emulators.*
15. [[putta-2024-agent-q]] — *Agent Q: MCTS-guided search → DPO. "Search is data."*

## Stage 4 — Reward Design & Verification

*How to get the right signal for agents in sparse-reward GUI tasks.*

16. [[cui-2026-agentic-reward-modeling]] — *VAGEN: active verifier agent probes environment for noise-free RLVR.*
17. [[zhang-2025-progrm]] — *ProgRM: automatic progress milestone extraction for dense rewards.*
18. [[xiong-2025-gui-pra]] — *GUI-PRA: process reward with dynamic memory of UI state changes.*
19. [[xi-2025-agentprm]] — *AgentPRM: Promise + Progress signals for step-level credit.*
20. [[hu-2026-faithful-mobile-gui]] — *Faithful-Agent: GuAE solves advantage collapse under sparse GUI rewards.*

## Stage 5 — World Models & Data Efficiency

*Overcoming the I/O wall through learned environment models.*

21. [[ding-2026-dynaweb]] — *DynaWeb: Web World Model; 1000× throughput via dream rollouts.*
22. [[luo-2025-vimo]] — *ViMo: visual GUI world model (image-level prediction).*
23. [[xu-2026-code2world]] — *Code2World: renderable code world model.*
24. [[xu-2026-mobile-world-model]] — *How Mobile World Model Guides GUI Agents: 4 modality comparison.*
25. [[zhang-2026-m2-miner]] — *M2-Miner: MCTS multi-agent data mining; 64× efficiency.*

## Stage 6 — Frontier: Continual RL & Multi-platform

*The next horizon: agents that keep improving as their environments evolve.*

26. [[liu-2026-continual-gui-agents]] — *First continual RL framework for GUI agents (GUI-AiF).*
27. [[guo-2026-acurl]] — *ACuRL: autonomous curriculum RL with zero human data.*
28. [[xu-2026-mobile-agent-v3-5]] — *Mobile-Agent-v3.5: MRPO multi-platform RL; OSWorld 56.5%.*
29. [[yang-2026-cgl]] — *CGL: continual GUI learning via RFT.*

---

## Key Concepts to Master Along the Way

- [[agentic-rl]]
- [[reinforcement-learning-with-verifiable-rewards]]
- [[group-relative-policy-optimization]]
- [[multi-turn-gui-rl]]
- [[gui-io-wall]]
- [[gui-world-model]]
- [[api-gui-hybrid-action-space]]
- [[gui-grounding]]
