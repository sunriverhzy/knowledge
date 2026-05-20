---
title: "WebRL: Training LLM Web Agents via Self-evolving Online Curriculum Reinforcement Learning"
slug: "qi-2024-webrl"
year: 2024
venue: "ICLR 2025"
arxiv: "2411.02337"
domain: agent
tags: [agent, web-agent, online-rl, curriculum, reward-model]
status: read
ingested: 2026-05-20
category: rl-method
---

# WebRL: Training LLM Web Agents via Self-evolving Online Curriculum Reinforcement Learning

## TL;DR（一句话总结）
> web agent 的 self-evolving online curriculum RL：失败集策略生成失败任务的简化版本；ORM 判断轨迹成功；Llama-3.1-8B 在 WebArena 从 18.6% 提升到 42.4%。

## 主要贡献
- Self-evolving curriculum：从成功自动生成更难任务，从失败生成更简单任务
- Outcome-supervised Reward Model（ORM）：训练模型判断轨迹成功
- KL 约束 + experience replay 防止策略漂移
- 在 WebArena 上 42.4%（Llama-3.1-8B；发布时 2024 SOTA）

## 关联
- **相关概念：**
- [[gui-agent]]  
- [[reinforcement-learning-with-verifiable-rewards]]  
- [[multi-turn-gui-rl]]
- **Wiki 中的参见：** *（随 wiki 增长填入）*

## 个人笔记
*（在此添加笔记）*

## BibTeX
```bibtex
@article{qi2024webrl,
  title  = {WebRL: Training LLM Web Agents via Self-evolving Online Curriculum Reinforcement Learning},
  year   = {2024},
  url    = {https://arxiv.org/abs/2411.02337}
}
```
