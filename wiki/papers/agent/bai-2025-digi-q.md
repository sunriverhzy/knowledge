---
title: "Digi-Q: Learning Q-value Functions for Training Device-control Agents"
slug: "bai-2025-digi-q"
year: 2025
venue: "arXiv 2025"
arxiv: "2502.15760"
domain: agent
tags: [agent, gui-agent, mobile, offline-rl, q-learning, best-of-n]
status: read
ingested: 2026-05-20
category: rl-method
---

# Digi-Q: Learning Q-value Functions for Training Device-control Agents

## TL;DR
> Pure offline Q-value learning for device-control: frozen VLM backbone + MLP Q-head trained with IQL/CQL; Best-of-N reranking at inference substitutes expensive online interaction.

## Key Contributions
- Frozen VLM backbone + lightweight MLP Q-head: avoids visual forgetting
- IQL/CQL training on static AitW device-control data
- Best-of-N action reranking: sample N actions, select highest-value
- 21.2% improvement over prior offline methods on AitW

## Connections
- **Related concepts:**
- [[bai-2024-digiirl]]  
- [[gui-agent]]  
- [[reinforcement-learning-with-verifiable-rewards]]
- **See also in wiki:** *(fill as wiki grows)*

## Personal Notes
*(add notes here)*

## BibTeX
```bibtex
@article{bai2025digiq,
  title  = {Digi-Q: Learning Q-value Functions for Training Device-control Agents},
  year   = {2025},
  url    = {https://arxiv.org/abs/2502.15760}
}
```
