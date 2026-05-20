---
title: "OSWorld: Benchmarking Multimodal Agents for Open-ended Tasks in Real Computer Environments"
slug: "xie-2024-osworld"
year: 2024
venue: "NeurIPS 2024"
arxiv: "2404.07972"
domain: agent
tags: [agent, gui-agent, computer-use, benchmark, desktop]
status: read
ingested: 2026-05-20
category: benchmark
---

# OSWorld: Benchmarking Multimodal Agents for Open-ended Tasks in Real Computer Environments

## TL;DR（一句话总结）
> 第一个可扩展的真实电脑多模态 agent 环境：369 个开放 Linux 桌面任务，通过执行实际副作用（文件状态、DB 行、UI 状态）而非文本匹配进行评测。

## 主要贡献
- 真实 Linux VM（Docker/QEMU）；execution-based 评测检查文件状态、DOM、DB
- 369 个多样任务，覆盖 9 种应用（浏览器、文件管理、终端、Calc、GIMP 等）
- 人机差距：人类 72.4% vs 当时最佳 agent ~12%
- 确立了 GUI agent 的标准 desktop RL / 评测平台

## 关联
- **相关概念：**
- [[gui-agent]]  
- [[computer-use-agent]]  
- [[reinforcement-learning-with-verifiable-rewards]]
- **Wiki 中的参见：** *（随 wiki 增长填入）*

## 个人笔记
*（在此添加笔记）*

## BibTeX
```bibtex
@article{xie2024osworld,
  title  = {OSWorld: Benchmarking Multimodal Agents for Open-ended Tasks in Real Computer Environments},
  year   = {2024},
  url    = {https://arxiv.org/abs/2404.07972}
}
```
