---
title: "Executable Agentic Memory for GUI Agent"
slug: "qin-2026-executable-agentic-memory"
year: 2026
venue: "arXiv 2026"
arxiv: "2605.12294"
domain: agent
tags: [agent, gui-agent, memory, knowledge-graph, mcts, long-horizon]
status: read
ingested: 2026-05-20
category: frontier
---

# Executable Agentic Memory for GUI Agent

## TL;DR
> EAM: a Knowledge-Graph-based executable memory that shifts GUI planning from free-form LLM generation to retrieval-and-execution over graph; built via state-aware DFS + action-group mining; Q-guided MCTS search; +19.6% over UI-TARS-7B on AndroidWorld.

## Key Contributions
- Executable Agentic Memory (EAM): structured KG that stores multi-step GUI routines as executable nodes
- State-aware DFS + action-group mining builds sample-efficient memory from limited demonstrations
- Q-function-guided MCTS over KG enables principled path search; theoretical bias-consistency proof and sample complexity bound
- +19.6% over UI-TARS-7B on AndroidWorld; 6× token cost reduction vs GPT-4o; 2.8s average latency

## Connections
- **Related concepts:**
- [[gui-agent]]
- [[multi-turn-gui-rl]]
- [[reinforcement-learning-with-verifiable-rewards]]
- **See also in wiki:** *(fill as wiki grows)*

## Personal Notes
*(add notes here)*

## BibTeX
```bibtex
@article{qin2026executableagenticmemory,
  title  = {Executable Agentic Memory for GUI Agent},
  year   = {2026},
  url    = {https://arxiv.org/abs/2605.12294}
}
```
