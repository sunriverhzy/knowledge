---
title: "GUI-G²: Gaussian Reward Modeling for GUI Grounding"
slug: "tang-2025-gui-g2"
year: 2025
venue: "arXiv 2025"
arxiv: "2507.15846"
domain: agent
tags: [agent, gui-agent, grounding, gaussian-reward, reward-shaping]
status: read
ingested: 2026-05-20
category: grounding
---

# GUI-G²: Gaussian Reward Modeling for GUI Grounding

## TL;DR（一句话总结）
> GUI grounding 的 Gaussian 奖励建模：用以目标元素为中心的 Gaussian 概率替换二元 hit/miss；解决[1 像素偏差 = 失败]问题。

## 主要贡献
- Gaussian 奖励：r = exp(-‖pred - target‖²/2σ²)；目标附近的稠密梯度
- 解决坐标回归的脆弱二元 hit/miss 奖励
- 带目标 bounding box 附近稠密奖励的稳定训练

## 关联
- **相关概念：**
- [[gui-grounding]]  
- [[reinforcement-learning-with-verifiable-rewards]]
- **Wiki 中的参见：** *（随 wiki 增长填入）*

## 个人笔记
*（在此添加笔记）*

## BibTeX
```bibtex
@article{tang2025guig2,
  title  = {GUI-G²: Gaussian Reward Modeling for GUI Grounding},
  year   = {2025},
  url    = {https://arxiv.org/abs/2507.15846}
}
```
