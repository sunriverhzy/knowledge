---
title: "SE-GUI: Enhancing Visual Grounding for GUI Agents via Self-evolutionary Reinforcement Learning"
slug: "yuan-2025-se-gui"
year: 2025
venue: "NeurIPS 2025"
arxiv: "2505.12370"
domain: agent
tags: [agent, gui-agent, grounding, self-evolutionary-rl, attention, pseudo-labels]
status: read
ingested: 2026-05-20
category: grounding
---

# SE-GUI: Enhancing Visual Grounding for GUI Agents via Self-evolutionary Reinforcement Learning

## TL;DR（一句话总结）
> GUI grounding 的 self-evolutionary RL：用 attention map 作为中间监督（伪标签）引导坐标预测，无需人工标注。

## 主要贡献
- 基于注意力的伪标签：提取空间注意力识别模型关注的区域
- Self-evolutionary 训练循环：伪标签 → RL 信号 → 改进模型 → 更好的伪标签
- 减少对昂贵 grounding 标注数据的依赖
- NeurIPS 2025

## 关联
- **相关概念：**
- [[gui-grounding]]  
- [[reinforcement-learning-with-verifiable-rewards]]
- **Wiki 中的参见：** *（随 wiki 增长填入）*

## 个人笔记
*（在此添加笔记）*

## BibTeX
```bibtex
@article{yuan2025segui,
  title  = {SE-GUI: Enhancing Visual Grounding for GUI Agents via Self-evolutionary Reinforcement Learning},
  year   = {2025},
  url    = {https://arxiv.org/abs/2505.12370}
}
```
