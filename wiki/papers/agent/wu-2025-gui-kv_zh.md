---
title: "GUI-KV: Efficient GUI Agents via KV Cache with Spatio-Temporal Awareness"
slug: "wu-2025-gui-kv"
year: 2025
venue: "ICLR 2026"
arxiv: "2510.00536"
domain: agent
tags: [agent, gui-agent, efficiency, kv-cache, inference, spatio-temporal, iclr]
status: read
ingested: 2026-05-20
category: frontier
---

# GUI-KV: Efficient GUI Agents via KV Cache with Spatio-Temporal Awareness

## TL;DR（一句话总结）
> GUI-KV：利用截图序列中空间和时间冗余的 GUI agent 即插即用 KV cache 压缩；无需重训练；在适度缓存预算下保持准确率；ICLR 2026。

## 主要贡献
- 空间 KV 压缩：裁剪静态、非交互 UI 区域的 KV 对
- 时间 KV 压缩：对跨步骤未变化的屏幕区域去重 KV 条目
- 即插即用：无需重训练；与任意 transformer GUI agent 兼容
- ICLR 2026 接收；推理速度显著提升，准确率损失极小

## 关联
- **相关概念：**
- [[gui-agent]]
- [[gui-io-wall]]
- [[gui-grounding]]
- **Wiki 中的参见：** *（随 wiki 增长填入）*

## 个人笔记
*（在此添加笔记）*

## BibTeX
```bibtex
@article{wu2025guikv,
  title  = {GUI-KV: Efficient GUI Agents via KV Cache with Spatio-Temporal Awareness},
  year   = {2025},
  url    = {https://arxiv.org/abs/2510.00536}
}
```
