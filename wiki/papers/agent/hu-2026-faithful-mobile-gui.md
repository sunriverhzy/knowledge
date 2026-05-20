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

## TL;DR
> Faithful-Agent: two-stage framework for faithfulness in mobile GUI agents; SFT with evidence-perturbation training + Guided Advantage Estimator (GuAE) built on GRPO to combat advantage collapse under sparse GUI rewards; improves Trap SR from 13.88% to 80.21%.

## Key Contributions
- Faithfulness-first paradigm: agent must ground actions in current screen evidence, not memory shortcuts
- Stage 1 SFT: trains on evidence-perturbed scenarios to learn refusal/avoidance of blind operations
- Stage 2 GuAE: anchor-based, variance-adaptive advantage tempering on GRPO to prevent advantage collapse
- Thought-action consistency reward ensures reasoning trace aligns with executed action
- Trap SR: 13.88% → 80.21%; maintains general instruction-following performance

## Connections
- **Related concepts:**
- [[gui-agent]]
- [[group-relative-policy-optimization]]
- [[reinforcement-learning-with-verifiable-rewards]]
- **See also in wiki:** *(fill as wiki grows)*

## Personal Notes
*(add notes here)*

## BibTeX
```bibtex
@article{hu2026faithfulmobilegui,
  title  = {Faithful Mobile GUI Agents with Guided Advantage Estimator},
  year   = {2026},
  url    = {https://arxiv.org/abs/2605.01208}
}
```
