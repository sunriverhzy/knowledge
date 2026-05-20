---
title: "Faithful Mobile GUI Agents with Guided Advantage Estimator"
slug: "hu-2026-faithful-mobile-gui"
year: 2026
venue: "arXiv 2026"
arxiv: "2605.01208"
domain: agent
tags: [agent, mobile, gui-agent, rl, grpo, faithfulness, advantage-estimator]
status: read
ingested: 2026-05-20
category: rl-method
---

# Faithful Mobile GUI Agents with Guided Advantage Estimator

## TL;DR（一句话总结）
> Faithful-Agent：mobile GUI agent 忠实性两阶段框架；证据扰动 SFT + 基于 GRPO 构建的 Guided Advantage Estimator（GuAE），对抗稀疏 GUI 奖励下的 advantage collapse；Trap SR 从 13.88% 提升到 80.21%。

## 主要贡献
- 忠实性优先范式：agent 必须基于当前屏幕证据而非记忆捷径来执行动作
- 第 1 阶段 SFT：在证据扰动场景上训练，学习拒绝/避免盲目操作
- 第 2 阶段 GuAE：GRPO 上基于锚点的方差自适应 advantage tempering，防止 advantage collapse
- Thought-action consistency reward 确保推理轨迹与执行动作一致
- Trap SR：13.88% → 80.21%；保持通用指令跟随能力

## 关联
- **相关概念：**
- [[gui-agent]]
- [[group-relative-policy-optimization]]
- [[reinforcement-learning-with-verifiable-rewards]]
- **Wiki 中的参见：** *（随 wiki 增长填入）*

## 个人笔记
*（在此添加笔记）*

## BibTeX
```bibtex
@article{hu2026faithfulmobilegui,
  title  = {Faithful Mobile GUI Agents with Guided Advantage Estimator},
  year   = {2026},
  url    = {https://arxiv.org/abs/2605.01208}
}
```
