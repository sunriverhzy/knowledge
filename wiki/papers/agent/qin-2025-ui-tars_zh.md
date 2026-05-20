---
title: "UI-TARS: Pioneering Automated GUI Interaction with Native Agents"
slug: "qin-2025-ui-tars"
year: 2025
venue: "arXiv 2025"
arxiv: "2501.12326"
domain: agent
tags: [agent, gui-agent, computer-use, multimodal, sft, rl, cross-platform]
status: read
ingested: 2026-05-20
category: frontier
---

# UI-TARS: Pioneering Automated GUI Interaction with Native Agents

## TL;DR（一句话总结）
> 原生截图-only GUI agent（无 accessibility API）；跨平台（Android, Windows, Web）；'thoughts-before-actions' 范式；SFT+RL（DPO / experience replay）；10+ GUI benchmark SOTA。

## 主要贡献
- 端到端 VLM，跨 3 个平台的统一动作空间（无 accessibility tree）
- Thoughts-before-actions：执行每个动作前先有 CoT 推理轨迹
- Experience replay：成功轨迹缓冲区，避免全零奖励 batch
- ARPO 变体（DPO+GRPO）在 OSWorld 达 20.4%；2B–72B 模型家族

## 关联
- **相关概念：**
- [[gui-agent]]  
- [[group-relative-policy-optimization]]  
- [[reinforcement-learning-with-verifiable-rewards]]  
- [[api-gui-hybrid-action-space]]
- **Wiki 中的参见：** *（随 wiki 增长填入）*

## 个人笔记
*（在此添加笔记）*

## BibTeX
```bibtex
@article{qin2025uitars,
  title  = {UI-TARS: Pioneering Automated GUI Interaction with Native Agents},
  year   = {2025},
  url    = {https://arxiv.org/abs/2501.12326}
}
```
