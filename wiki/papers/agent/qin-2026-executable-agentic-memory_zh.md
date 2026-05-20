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

## TL;DR（一句话总结）
> EAM：基于知识图谱的可执行记忆，把 GUI 规划从自由生成转变为图上的检索与执行；通过 state-aware DFS + action-group mining 构建；Q 引导 MCTS 搜索；在 AndroidWorld 上超过 UI-TARS-7B 19.6%。

## 主要贡献
- Executable Agentic Memory（EAM）：将多步 GUI 例程存储为可执行节点的结构化知识图谱
- State-aware DFS + action-group mining 从有限 demonstration 构建高样本效率的记忆
- Q 函数引导的知识图谱 MCTS 实现原则化路径搜索；提供理论 bias-consistency 证明和样本复杂度界
- 在 AndroidWorld 上超过 UI-TARS-7B 19.6%；相比 GPT-4o 降低 6× token 成本；平均延迟 2.8 秒

## 关联
- **相关概念：**
- [[gui-agent]]
- [[multi-turn-gui-rl]]
- [[reinforcement-learning-with-verifiable-rewards]]
- **Wiki 中的参见：** *（随 wiki 增长填入）*

## 个人笔记
*（在此添加笔记）*

## BibTeX
```bibtex
@article{qin2026executableagenticmemory,
  title  = {Executable Agentic Memory for GUI Agent},
  year   = {2026},
  url    = {https://arxiv.org/abs/2605.12294}
}
```
