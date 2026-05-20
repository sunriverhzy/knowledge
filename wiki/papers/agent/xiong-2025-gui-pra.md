---
title: "GUI-PRA: Process Reward Agent for GUI Tasks"
slug: "xiong-2025-gui-pra"
year: 2025
venue: "ICLR 2026"
arxiv: "openreview-k5cQgBOyJO"
domain: agent
tags: [agent, gui-agent, reward-modeling, process-reward, dynamic-memory, iclr]
status: read
ingested: 2026-05-20
category: rl-method
---

# GUI-PRA: Process Reward Agent for GUI Tasks

## TL;DR
> GUI-PRA: judge agent for process rewards in GUI tasks; overcomes 'lost in the middle' and UI-state-change blindness of standard PRMs by maintaining dynamic memory of GUI state changes and UI interactions.

## Key Contributions
- Dynamic memory of GUI state changes: tracks which UI elements changed, appeared, or disappeared
- Overcomes 'lost in the middle': PRM attends to all steps rather than weighting early/late steps disproportionately
- Process rewards for GUI tasks: dense feedback at each step rather than terminal-only
- ICLR 2026 accepted

## Connections
- **Related concepts:**
- [[multi-turn-gui-rl]]
- [[reinforcement-learning-with-verifiable-rewards]]
- [[gui-agent]]
- **See also in wiki:** *(fill as wiki grows)*

## Personal Notes
*(add notes here)*

## BibTeX
```bibtex
@article{xiong2025guipra,
  title  = {GUI-PRA: Process Reward Agent for GUI Tasks},
  year   = {2025},
  url    = {https://arxiv.org/abs/openreview-k5cQgBOyJO}
}
```
