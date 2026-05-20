---
title: "UI-TARS: Pioneering Automated GUI Interaction with Native Agents"
slug: "qin-2025-ui-tars"
year: 2025
venue: "arXiv 2025"
arxiv: "2501.12326"
domain: agent
tags: [agent, gui-agent, computer-use, multimodal, sft, rl, cross-platform]
status: read
ingested: 2026-05-20
category: frontier
---

# UI-TARS: Pioneering Automated GUI Interaction with Native Agents

## TL;DR
> Native screenshot-only GUI agent (no accessibility API); cross-platform (Android, Windows, Web); 'thoughts-before-actions' paradigm; SFT+RL with DPO/experience-replay; SOTA on 10+ GUI benchmarks.

## Key Contributions
- End-to-end VLM with unified action space across 3 platforms (no accessibility tree)
- Thoughts-before-actions: CoT reasoning trace before executing each action
- Experience replay: buffer of successful trajectories to avoid zero-gradient batches
- ARPO variant (DPO+GRPO) reaches 20.4% on OSWorld; 2B–72B model family

## Connections
- **Related concepts:**
- [[gui-agent]]  
- [[group-relative-policy-optimization]]  
- [[reinforcement-learning-with-verifiable-rewards]]  
- [[api-gui-hybrid-action-space]]
- **See also in wiki:** *(fill as wiki grows)*

## Personal Notes
*(add notes here)*

## BibTeX
```bibtex
@article{qin2025uitars,
  title  = {UI-TARS: Pioneering Automated GUI Interaction with Native Agents},
  year   = {2025},
  url    = {https://arxiv.org/abs/2501.12326}
}
```
