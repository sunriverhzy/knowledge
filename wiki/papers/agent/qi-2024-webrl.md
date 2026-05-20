---
title: "WebRL: Training LLM Web Agents via Self-evolving Online Curriculum Reinforcement Learning"
slug: "qi-2024-webrl"
year: 2024
venue: "ICLR 2025"
arxiv: "2411.02337"
domain: agent
tags: [agent, web-agent, online-rl, curriculum, reward-model]
status: read
ingested: 2026-05-20
category: rl-method
---

# WebRL: Training LLM Web Agents via Self-evolving Online Curriculum Reinforcement Learning

## TL;DR
> Self-evolving online curriculum RL for web agents: failure-set strategy generates simplified variants of failed tasks; ORM judges trajectory success; Llama-3.1-8B from 18.6% to 42.4% on WebArena.

## Key Contributions
- Self-evolving curriculum: auto-generates harder tasks from successes, simpler from failures
- Outcome-supervised Reward Model (ORM): train model to judge trajectory success
- KL constraints + experience replay prevent policy drift
- 42.4% on WebArena (Llama-3.1-8B; 2024 SOTA at release)

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
@article{qi2024webrl,
  title  = {WebRL: Training LLM Web Agents via Self-evolving Online Curriculum Reinforcement Learning},
  year   = {2024},
  url    = {https://arxiv.org/abs/2411.02337}
}
```
