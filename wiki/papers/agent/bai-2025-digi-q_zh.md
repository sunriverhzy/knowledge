---
title: "Digi-Q: Learning Q-value Functions for Training Device-control Agents"
slug: "bai-2025-digi-q"
year: 2025
venue: "arXiv 2025"
arxiv: "2502.15760"
domain: agent
tags: [agent, gui-agent, mobile, offline-rl, q-learning, best-of-n]
status: read
ingested: 2026-05-20
category: rl-method
---

# Digi-Q: Learning Q-value Functions for Training Device-control Agents

## TL;DR（一句话总结）
> 纯 offline Q-value learning for device-control：冻结 VLM backbone + MLP Q-head（用 IQL/CQL 训练）；推理时 Best-of-N reranking 替代昂贵的在线交互。

## 主要贡献
- 冻结 VLM backbone + 轻量 MLP Q-head：避免视觉遗忘
- 在静态 AitW device-control 数据上用 IQL/CQL 训练
- Best-of-N action reranking：采样 N 个动作，选最高价值
- 在 AitW 上比先前 offline 方法提升 21.2%

## 关联
- **相关概念：**
- [[bai-2024-digiirl]]  
- [[gui-agent]]  
- [[reinforcement-learning-with-verifiable-rewards]]
- **Wiki 中的参见：** *（随 wiki 增长填入）*

## 个人笔记
*（在此添加笔记）*

## BibTeX
```bibtex
@article{bai2025digiq,
  title  = {Digi-Q: Learning Q-value Functions for Training Device-control Agents},
  year   = {2025},
  url    = {https://arxiv.org/abs/2502.15760}
}
```
