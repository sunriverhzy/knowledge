---
title: "GUI-G²: Gaussian Reward Modeling for GUI Grounding"
slug: "tang-2025-gui-g2"
year: 2025
venue: "arXiv 2025"
arxiv: "2507.15846"
domain: agent
tags: [agent, gui-agent, grounding, gaussian-reward, reward-shaping]
status: read
ingested: 2026-05-20
category: grounding
---

# GUI-G²: Gaussian Reward Modeling for GUI Grounding

## TL;DR
> Gaussian reward modeling for GUI grounding: replaces binary hit/miss with a Gaussian probability centered at target element; solves '1-pixel deviation = failure' problem.

## Key Contributions
- Gaussian reward: r = exp(-‖pred - target‖²/2σ²); dense gradient near target
- Addresses the brittle binary hit/miss reward for coordinate regression
- Stable training with dense rewards near target bounding boxes

## Connections
- **Related concepts:**
- [[gui-grounding]]  
- [[reinforcement-learning-with-verifiable-rewards]]
- **See also in wiki:** *(fill as wiki grows)*

## Personal Notes
*(add notes here)*

## BibTeX
```bibtex
@article{tang2025guig2,
  title  = {GUI-G²: Gaussian Reward Modeling for GUI Grounding},
  year   = {2025},
  url    = {https://arxiv.org/abs/2507.15846}
}
```
