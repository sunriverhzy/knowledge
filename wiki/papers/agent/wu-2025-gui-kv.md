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

## TL;DR
> GUI-KV: plug-and-play KV cache compression for GUI agents exploiting spatial and temporal redundancy in screenshot sequences; no retraining required; maintains accuracy at modest cache budgets.

## Key Contributions
- Spatial KV compression: prune KV pairs for static, non-interactive UI regions
- Temporal KV compression: de-duplicate KV entries for unchanged screen regions across steps
- Plug-and-play: no retraining; compatible with any transformer-based GUI agent
- ICLR 2026 accepted; significant inference speedup with minimal accuracy loss

## Connections
- **Related concepts:**
- [[gui-agent]]
- [[gui-io-wall]]
- [[gui-grounding]]
- **See also in wiki:** *(fill as wiki grows)*

## Personal Notes
*(add notes here)*

## BibTeX
```bibtex
@article{wu2025guikv,
  title  = {GUI-KV: Efficient GUI Agents via KV Cache with Spatio-Temporal Awareness},
  year   = {2025},
  url    = {https://arxiv.org/abs/2510.00536}
}
```
