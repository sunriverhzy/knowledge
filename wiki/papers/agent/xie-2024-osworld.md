---
title: "OSWorld: Benchmarking Multimodal Agents for Open-ended Tasks in Real Computer Environments"
slug: "xie-2024-osworld"
year: 2024
venue: "NeurIPS 2024"
arxiv: "2404.07972"
domain: agent
tags: [agent, gui-agent, computer-use, benchmark, desktop]
status: read
ingested: 2026-05-20
category: benchmark
---

# OSWorld: Benchmarking Multimodal Agents for Open-ended Tasks in Real Computer Environments

## TL;DR
> The first scalable real-computer environment for multimodal agents: 369 open-ended tasks across Linux desktop apps, evaluated by executing actual side-effects (file state, DB rows, UI state) rather than text matching.

## Key Contributions
- Real Linux VM with Docker/QEMU; execution-based evaluation checking file state, DOM, DB
- 369 diverse tasks spanning 9 apps (web browser, file manager, terminal, Calc, GIMP, etc.)
- Human performance gap: humans 72.4% vs best agent at time ~12%
- Establishes the standard desktop RL/eval platform for GUI agents

## Connections
- **Related concepts:**
- [[gui-agent]]  
- [[computer-use-agent]]  
- [[reinforcement-learning-with-verifiable-rewards]]
- **See also in wiki:** *(fill as wiki grows)*

## Personal Notes
*(add notes here)*

## BibTeX
```bibtex
@article{xie2024osworld,
  title  = {OSWorld: Benchmarking Multimodal Agents for Open-ended Tasks in Real Computer Environments},
  year   = {2024},
  url    = {https://arxiv.org/abs/2404.07972}
}
```
