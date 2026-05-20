---
title: "ARPO: End-to-End Policy Optimization for GUI Agents with Experience Replay"
slug: "lu-2025-arpo"
year: 2025
venue: "arXiv 2025"
arxiv: "2505.16282"
domain: agent
tags: [agent, gui-agent, dpo, grpo, experience-replay, offline-rl]
status: read
ingested: 2026-05-20
category: rl-method
---

# ARPO: End-to-End Policy Optimization for GUI Agents with Experience Replay

## TL;DR
> Combines DPO + GRPO with experience replay for GUI agents; addresses the zero-reward batch problem; 20.4% on OSWorld.

## Key Contributions
- ARPO = DPO preference optimization + GRPO online policy update
- Experience replay buffer: sample successful trajectories when current batch is all-failure
- Addresses the sparse-reward cold-start problem
- 20.4% on OSWorld (state-of-the-art for UI-TARS family)

## Connections
- **Related concepts:**
- [[group-relative-policy-optimization]]  
- [[qin-2025-ui-tars]]  
- [[reinforcement-learning-with-verifiable-rewards]]
- **See also in wiki:** *(fill as wiki grows)*

## Personal Notes
*(add notes here)*

## BibTeX
```bibtex
@article{lu2025arpo,
  title  = {ARPO: End-to-End Policy Optimization for GUI Agents with Experience Replay},
  year   = {2025},
  url    = {https://arxiv.org/abs/2505.16282}
}
```
