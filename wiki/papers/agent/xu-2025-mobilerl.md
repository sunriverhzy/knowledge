---
title: "MobileRL: Online Agentic Reinforcement Learning for Mobile GUI Agents"
slug: "xu-2025-mobilerl"
year: 2025
venue: "arXiv 2025"
arxiv: "2509.18119"
domain: agent
tags: [agent, mobile, gui-agent, online-rl, curriculum, difficulty-adaptive]
status: read
ingested: 2026-05-20
category: rl-method
---

# MobileRL: Online Agentic Reinforcement Learning for Mobile GUI Agents

## TL;DR
> Online RL for mobile GUI agents: AdaGRPO with difficulty weighting, Shortest-Path Reward Adjustment (SPA) to suppress redundant actions, Failure Curriculum Filtering.

## Key Contributions
- AdaGRPO: difficulty-weighted GRPO (lower success rate → higher gradient weight)
- SPA: penalizes longer-than-necessary action sequences to suppress reward hacking
- FCF: removes persistently-failing tasks from curriculum
- Strong performance on AndroidWorld

## Connections
- **Related concepts:**
- [[gui-agent]]  
- [[reinforcement-learning-with-verifiable-rewards]]  
- [[multi-turn-gui-rl]]
- **See also in wiki:** *(fill as wiki grows)*

## Personal Notes
*(add notes here)*

## BibTeX
```bibtex
@article{xu2025mobilerl,
  title  = {MobileRL: Online Agentic Reinforcement Learning for Mobile GUI Agents},
  year   = {2025},
  url    = {https://arxiv.org/abs/2509.18119}
}
```
