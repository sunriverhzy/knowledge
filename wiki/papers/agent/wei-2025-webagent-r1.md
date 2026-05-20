---
title: "WebAgent-R1: Training Web Agents via End-to-end Multi-turn Reinforcement Learning"
slug: "wei-2025-webagent-r1"
year: 2025
venue: "EMNLP 2025"
arxiv: "2505.16421"
domain: agent
tags: [agent, web-agent, multi-turn, grpo, m-grpo, online-rl]
status: read
ingested: 2026-05-20
category: rl-method
---

# WebAgent-R1: Training Web Agents via End-to-end Multi-turn Reinforcement Learning

## TL;DR
> Multi-Turn GRPO (M-GRPO): treats entire web interaction trajectories as optimization samples; enables learning 'delayed gratification' across long horizons.

## Key Contributions
- M-GRPO: trajectory-level GRPO — whole trajectory gets one advantage estimate
- Dynamic context compression: per-step observation summaries prevent context overflow
- Parallel trajectory rollout improves training diversity
- Outperforms single-step GRPO on WebArena multi-step tasks

## Connections
- **Related concepts:**
- [[multi-turn-gui-rl]]  
- [[group-relative-policy-optimization]]  
- [[gui-agent]]
- **See also in wiki:** *(fill as wiki grows)*

## Personal Notes
*(add notes here)*

## BibTeX
```bibtex
@article{wei2025webagentr1,
  title  = {WebAgent-R1: Training Web Agents via End-to-end Multi-turn Reinforcement Learning},
  year   = {2025},
  url    = {https://arxiv.org/abs/2505.16421}
}
```
