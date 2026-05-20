---
title: "How Mobile World Model Guides GUI Agents?"
slug: "xu-2026-mobile-world-model"
year: 2026
venue: "arXiv 2026"
arxiv: "2605.10347"
domain: agent
tags: [agent, mobile, gui-agent, world-model, gui-world-model, code2world]
status: read
ingested: 2026-05-20
category: mobile
---

# How Mobile World Model Guides GUI Agents?

## TL;DR
> Empirically studies four mobile world model modalities (delta-text, full-text, diffusion-image, renderable code) and how each guides mobile GUI agents; finds renderable code best for training, text more robust for OOD online execution.

## Key Contributions
- Trains four world model variants on filtered mobile interaction data; evaluates on MobileWorldBench and Code2WorldBench
- Renderable code reconstruction achieves high in-distribution fidelity and provides multimodal supervision for data construction
- Text feedback is more robust than image prediction in OOD online execution environments
- World-model-generated trajectories improve downstream agent performance even when not perfectly on-distribution
- Posterior self-reflection via world model limited for over-confident agents; better as prior perception or training supervision

## Connections
- **Related concepts:**
- [[gui-world-model]]
- [[gui-agent]]
- [[gui-io-wall]]
- **See also in wiki:** *(fill as wiki grows)*

## Personal Notes
*(add notes here)*

## BibTeX
```bibtex
@article{xu2026mobileworldmodel,
  title  = {How Mobile World Model Guides GUI Agents?},
  year   = {2026},
  url    = {https://arxiv.org/abs/2605.10347}
}
```
