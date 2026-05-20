---
title: "How Mobile World Model Guides GUI Agents?"
slug: "xu-2026-mobile-world-model"
year: 2026
venue: "arXiv 2026"
arxiv: "2605.10347"
domain: agent
tags: [agent, mobile, gui-agent, world-model, gui-world-model, code2world]
status: read
ingested: 2026-05-20
category: mobile
---

# How Mobile World Model Guides GUI Agents?

## TL;DR（一句话总结）
> 系统研究四种移动 world model 模态（delta-text、full-text、扩散图像、可渲染代码）对 mobile GUI agent 的指导效果；发现可渲染代码最适合训练阶段，文本对 OOD 在线执行更鲁棒。

## 主要贡献
- 在过滤的移动交互数据上训练四种 world model 变体，在 MobileWorldBench 和 Code2WorldBench 上评估
- 可渲染代码重建在分布内具有高保真度，并为数据构建提供多模态监督
- 文本反馈在 OOD 在线执行环境中比图像预测更鲁棒
- World model 生成的轨迹可提升下游 agent 性能，即便不完全保持原始分布
- 世界模型后验自反思对过度自信的 agent 收益有限；更适合作为先验感知或训练监督

## 关联
- **相关概念：**
- [[gui-world-model]]
- [[gui-agent]]
- [[gui-io-wall]]
- **Wiki 中的参见：** *（随 wiki 增长填入）*

## 个人笔记
*（在此添加笔记）*

## BibTeX
```bibtex
@article{xu2026mobileworldmodel,
  title  = {How Mobile World Model Guides GUI Agents?},
  year   = {2026},
  url    = {https://arxiv.org/abs/2605.10347}
}
```
