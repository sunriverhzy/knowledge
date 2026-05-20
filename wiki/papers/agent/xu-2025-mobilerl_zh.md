---
title: "MobileRL: Online Agentic Reinforcement Learning for Mobile GUI Agents"
slug: "xu-2025-mobilerl"
year: 2025
venue: "arXiv 2025"
arxiv: "2509.18119"
domain: agent
tags: [agent, mobile, gui-agent, online-rl, curriculum, difficulty-adaptive]
status: read
ingested: 2026-05-20
category: rl-method
---

# MobileRL: Online Agentic Reinforcement Learning for Mobile GUI Agents

## TL;DR（一句话总结）
> Mobile GUI agent 的 online RL：AdaGRPO 带难度加权；Shortest-Path Reward Adjustment（SPA）抑制冗余操作；Failure Curriculum Filtering。

## 主要贡献
- AdaGRPO：难度加权的 GRPO（成功率越低 → 梯度权重越高）
- SPA：惩罚超出必要长度的动作序列，抑制 reward hacking
- FCF：从 curriculum 中移除持续失败的任务
- 在 AndroidWorld 上强性能

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
@article{xu2025mobilerl,
  title  = {MobileRL: Online Agentic Reinforcement Learning for Mobile GUI Agents},
  year   = {2025},
  url    = {https://arxiv.org/abs/2509.18119}
}
```
