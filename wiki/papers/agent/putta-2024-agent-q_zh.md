---
title: "Agent Q: Advanced Reasoning and Learning for Autonomous AI Agents"
slug: "putta-2024-agent-q"
year: 2024
venue: "arXiv 2024"
arxiv: "2408.07199"
domain: agent
tags: [agent, gui-agent, web, offline-rl, dpo, mcts, web-agent]
status: read
ingested: 2026-05-20
category: rl-method
---

# Agent Q: Advanced Reasoning and Learning for Autonomous AI Agents

## TL;DR（一句话总结）
> 用 MCTS 引导轨迹搜索生成高质量 DPO 训练数据：[search is data]；Llama-3-70B 网络预订从 18.6% 提升到 81.7%。

## 主要贡献
- 带 value-model 剪枝的 guided MCTS 用于高质量 web 轨迹搜索
- Self-critique 机制：树搜索过程中的 AI 状态评估
- MCTS 路径 → DPO 偏好对进行 offline 策略训练
- 真实网络预订（OpenTable）相对提升 340%

## 关联
- **相关概念：**
- [[gui-agent]]  
- [[reinforcement-learning-with-verifiable-rewards]]  
- [[group-relative-policy-optimization]]
- **Wiki 中的参见：** *（随 wiki 增长填入）*

## 个人笔记
*（在此添加笔记）*

## BibTeX
```bibtex
@article{putta2024agentq,
  title  = {Agent Q: Advanced Reasoning and Learning for Autonomous AI Agents},
  year   = {2024},
  url    = {https://arxiv.org/abs/2408.07199}
}
```
