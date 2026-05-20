---
title: "WebAgent-R1: Training Web Agents via End-to-end Multi-turn Reinforcement Learning"
slug: "wei-2025-webagent-r1"
year: 2025
venue: "EMNLP 2025"
arxiv: "2505.16421"
domain: agent
tags: [agent, web-agent, multi-turn, grpo, m-grpo, online-rl]
status: read
ingested: 2026-05-20
category: rl-method
---

# WebAgent-R1: Training Web Agents via End-to-end Multi-turn Reinforcement Learning

## TL;DR（一句话总结）
> Multi-Turn GRPO（M-GRPO）：把完整 web 交互轨迹当作优化样本；在长时程中学习[延迟满足]。

## 主要贡献
- M-GRPO：轨迹级 GRPO——整条轨迹获得一个 advantage 估计
- Dynamic context compression：每步观察摘要防止上下文溢出
- 并行轨迹 rollout 提升训练多样性
- 在 WebArena 多步任务上优于单步 GRPO

## 关联
- **相关概念：**
- [[multi-turn-gui-rl]]  
- [[group-relative-policy-optimization]]  
- [[gui-agent]]
- **Wiki 中的参见：** *（随 wiki 增长填入）*

## 个人笔记
*（在此添加笔记）*

## BibTeX
```bibtex
@article{wei2025webagentr1,
  title  = {WebAgent-R1: Training Web Agents via End-to-end Multi-turn Reinforcement Learning},
  year   = {2025},
  url    = {https://arxiv.org/abs/2505.16421}
}
```
