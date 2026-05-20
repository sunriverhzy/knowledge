---
title: "ARPO: End-to-End Policy Optimization for GUI Agents with Experience Replay"
slug: "lu-2025-arpo"
year: 2025
venue: "arXiv 2025"
arxiv: "2505.16282"
domain: agent
tags: [agent, gui-agent, dpo, grpo, experience-replay, offline-rl]
status: read
ingested: 2026-05-20
category: rl-method
---

# ARPO: End-to-End Policy Optimization for GUI Agents with Experience Replay

## TL;DR（一句话总结）
> 结合 DPO + GRPO + experience replay 用于 GUI agent；解决全零奖励 batch 问题；OSWorld 20.4%。

## 主要贡献
- ARPO = DPO 偏好优化 + GRPO 在线策略更新
- Experience replay 缓冲区：当前 batch 全部失败时从成功轨迹采样
- 解决稀疏奖励的冷启动问题
- 在 OSWorld 上 20.4%（UI-TARS 家族 SOTA）

## 关联
- **相关概念：**
- [[group-relative-policy-optimization]]  
- [[qin-2025-ui-tars]]  
- [[reinforcement-learning-with-verifiable-rewards]]
- **Wiki 中的参见：** *（随 wiki 增长填入）*

## 个人笔记
*（在此添加笔记）*

## BibTeX
```bibtex
@article{lu2025arpo,
  title  = {ARPO: End-to-End Policy Optimization for GUI Agents with Experience Replay},
  year   = {2025},
  url    = {https://arxiv.org/abs/2505.16282}
}
```
