---
title: "SE-GUI: Enhancing Visual Grounding for GUI Agents via Self-evolutionary Reinforcement Learning"
slug: "yuan-2025-se-gui"
year: 2025
venue: "NeurIPS 2025"
arxiv: "2505.12370"
domain: agent
tags: [agent, gui-agent, grounding, self-evolutionary-rl, attention, pseudo-labels]
status: read
ingested: 2026-05-20
category: grounding
---

# SE-GUI: Enhancing Visual Grounding for GUI Agents via Self-evolutionary Reinforcement Learning

## TL;DR
> Self-evolutionary RL for GUI grounding: uses attention maps as intermediate supervision (pseudo-labels) to guide coordinate prediction without manual annotations.

## Key Contributions
- Attention-based pseudo-labels: extract spatial attention to identify which regions the model attends to
- Self-evolutionary training loop: pseudo-labels → RL signal → improved model → better pseudo-labels
- Reduces dependency on expensive grounding annotation data
- NeurIPS 2025

## Connections
- **Related concepts:**
- [[gui-grounding]]  
- [[reinforcement-learning-with-verifiable-rewards]]
- **See also in wiki:** *(fill as wiki grows)*

## Personal Notes
*(add notes here)*

## BibTeX
```bibtex
@article{yuan2025segui,
  title  = {SE-GUI: Enhancing Visual Grounding for GUI Agents via Self-evolutionary Reinforcement Learning},
  year   = {2025},
  url    = {https://arxiv.org/abs/2505.12370}
}
```
