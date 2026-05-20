---
title: "DigiRL: Training In-the-wild Device-control Agents with Autonomous Reinforcement Learning"
slug: "bai-2024-digiirl"
year: 2024
venue: "NeurIPS 2024"
arxiv: "2406.11896"
domain: agent
tags: [agent, gui-agent, mobile, rl, offline-to-online, digiirl]
status: read
ingested: 2026-05-20
category: rl-method
---

# DigiRL: Training In-the-wild Device-control Agents with Autonomous Reinforcement Learning

## TL;DR
> Canonical offline-to-online RL framework for device-control agents: initialize from Android in the Wild demonstrations, then refine online with VLM evaluator as reward signal.

## Key Contributions
- Offline initialization from AitW (715K trajectories) via filtered BC / AWR
- Online fine-tuning using VLM-as-evaluator as automatic reward signal
- Pioneered VLMs as automatic task-completion judges (not humans)
- 67.2% success on AitW with online stage

## Connections
- **Related concepts:**
- [[gui-agent]]  
- [[reinforcement-learning-with-verifiable-rewards]]  
- [[bai-2025-digi-q]]
- **See also in wiki:** *(fill as wiki grows)*

## Personal Notes
*(add notes here)*

## BibTeX
```bibtex
@article{bai2024digiirl,
  title  = {DigiRL: Training In-the-wild Device-control Agents with Autonomous Reinforcement Learning},
  year   = {2024},
  url    = {https://arxiv.org/abs/2406.11896}
}
```
