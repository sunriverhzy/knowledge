---
title: "DigiRL: Training In-the-wild Device-control Agents with Autonomous Reinforcement Learning"
slug: "bai-2024-digiirl"
year: 2024
venue: "NeurIPS 2024"
arxiv: "2406.11896"
domain: agent
tags: [agent, gui-agent, mobile, rl, offline-to-online, digiirl]
status: read
ingested: 2026-05-20
category: rl-method
---

# DigiRL: Training In-the-wild Device-control Agents with Autonomous Reinforcement Learning

## TL;DR（一句话总结）
> device-control agent 的经典 offline-to-online RL 框架：从 Android in the Wild demonstration 初始化，再用 VLM evaluator 作为奖励在线微调。

## 主要贡献
- 从 AitW（715K 轨迹）通过 filtered BC / AWR 做 offline 初始化
- 用 VLM-as-evaluator 作为自动奖励信号进行在线微调
- 首创用 VLM 作为自动任务完成裁判（取代人工）
- 在线阶段在 AitW 上达到 67.2% 成功率

## 关联
- **相关概念：**
- [[gui-agent]]  
- [[reinforcement-learning-with-verifiable-rewards]]  
- [[bai-2025-digi-q]]
- **Wiki 中的参见：** *（随 wiki 增长填入）*

## 个人笔记
*（在此添加笔记）*

## BibTeX
```bibtex
@article{bai2024digiirl,
  title  = {DigiRL: Training In-the-wild Device-control Agents with Autonomous Reinforcement Learning},
  year   = {2024},
  url    = {https://arxiv.org/abs/2406.11896}
}
```
