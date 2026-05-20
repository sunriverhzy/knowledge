# AI Research Wiki — Field Overview

> **A living knowledge map of the field.**
>
> This page synthesizes the *current state of the wiki* into a high-level
> narrative. It should be updated when landmark papers are ingested or
> when a new research direction emerges that changes the shape of the map.
>
> When in doubt, update `wiki/index.md` first; only update this file when
> the ingest genuinely moves the needle on the field-level picture.

*Last updated: 2026-05-20 (37 latest GUI/CUA/mobile-use papers ingested; total Agent papers now 93)*

---

## Scope of This Wiki

This wiki focuses on research at the intersection of:

- **Large Language Models (LLM)**
- **Reinforcement Learning (RL)**, especially as applied to LLM alignment and agents
- **AI-Generated Content (AIGC)**, including diffusion models and watermarking
- **Multimodal Learning**, especially Vision-Language Models (VLM) and video understanding
- **LLM-based Agents**, including multi-agent systems and GUI agents
- **Reward Modeling and Alignment**

Peripheral topics (pure CV, pure NLP, pure RL theory) are admitted only
when they directly bear on one of the above.

---

## Research Threads (to be filled as wiki grows)

As papers are ingested, the following threads will be fleshed out.
Each thread should summarize **what the question is**, **what we know
so far**, and **what the open problems are**.

### Thread 1 — Training LLMs with RL
*Key concepts:* RLHF, DPO, GRPO, PPO, reward modeling, verifiable rewards
*Key papers:* *(to be filled)*
*Open questions:* *(to be filled)*

### Thread 2 — LLM Agents
*Key concepts:* [[gui-agent]], [[computer-use-agent]], [[gui-grounding]],
[[reinforcement-learning-with-verifiable-rewards]], [[multi-turn-gui-rl]],
[[gui-io-wall]], [[api-gui-hybrid-action-space]], [[gui-world-model]]
*Key papers (frontier models):*
- [[xie-2024-osworld]] — Real-computer benchmark; execution-based evaluation.
- [[qin-2025-ui-tars]] — Native screenshot GUI agent; SFT+RL; 10+ benchmark SOTA.
- [[lai-2025-computerrl]] — API-GUI hybrid; end-to-end CUA online RL.
- [[agashe-2025-agent-s2]] — Generalist-specialist CUA; OSWorld SOTA.
- [[wang-2025-opencua]] — Open foundations for CUAs.
*Key papers (RL methods):*
- [[bai-2024-digiirl]] — Offline-to-online RL canonical framework.
- [[luo-2025-gui-r1]] — GUI-R1: RLVR+GRPO; 3K samples; emergent reasoning.
- [[qi-2024-webrl]] — Self-evolving curriculum RL for web agents.
- [[wei-2025-webagent-r1]] — Multi-turn GRPO for web agents.
- [[ding-2026-dynaweb]] — Web world model; model-based RL.
- [[wu-2025-hi-agent]] — Hierarchical planner-executor; 87.9% AitW.
*Key papers (latest 2025-2026 additions):*
- [[xu-2026-mobile-world-model]] — Mobile world model modalities; renderable code best for training.
- [[qin-2026-executable-agentic-memory]] — KG-based executable memory (+19.6% on AndroidWorld).
- [[xu-2026-mobile-agent-v3-5]] — GUI-Owl-1.5; MRPO multi-platform RL; SOTA on 20+ benchmarks.
- [[liu-2026-continual-gui-agents]] — First continual RL framework for GUI agents.
- [[cui-2026-agentic-reward-modeling]] — VAGEN: active verifier agent for noise-free RLVR.
- [[zhang-2026-m2-miner]] — MCTS + multi-agent for 64× efficient mobile data mining.
- [[luo-2025-vimo]] — ViMo: first visual GUI world model (image output, not text).
- [[xu-2026-code2world]] — Code2World: renderable-code world model.
- [[hu-2026-faithful-mobile-gui]] — GuAE: prevents advantage collapse in sparse GUI RL.
*Open questions:* How do we reliably train long-horizon agents?
How do multi-agent systems fail? How to build world models faithful enough for cross-app OS tasks?
**Can continual learning agents stay current as apps evolve without catastrophic forgetting?**
**How do we provide dense process rewards without manual annotation?**
**When should GUI agents use API tools vs GUI actions?**

### Thread 3 — Reward Models as a Subject of Study
*Key concepts:* reward model quality, reward hacking, long-horizon reward
modeling, generative reward models, **reward variance**, **RM as teacher**,
process reward models
*Key papers:*
- [[hong-2025-think-rm]] — horizontal inference-time scaling for
  GenRMs; pairwise RLHF pipeline. (Improves the *judge*.)
- [[razin-2025-good-teacher]] — formalizes [[reward-variance]] as the
  missing axis of RM quality; proves RM quality is **policy-dependent**.
  (Reframes the *teacher*.)
- [[think-rm-vs-good-teacher]] — comparison page; distills the open
  question of combining long-CoT judgment with variance-aware training.

*Open questions:* What makes a reward model a good *teacher* (not
just a good classifier)? How do we align reward models with
non-verifiable tasks? Can **horizontal** (long single CoT) and
**vertical** (many short CoTs) inference-time scaling be combined
effectively in RMs? Can we train RMs with a *joint* accuracy + variance
objective, and can [[generative-reward-model]]s be evaluated through
the policy-conditional "teacher" lens?

### Thread 4 — Multimodal & Video Understanding
*Key concepts:* VLM, temporal grounding, video QA, perception at any resolution
*Key papers:* *(to be filled)*

### Thread 5 — AIGC & Watermarking
*Key concepts:* image/video watermarking, adversarial robustness,
watermark capacity, forgery attacks
*Key papers:* *(to be filled)*

### Thread 6 — Data Generation & Synthetic Data
*Key concepts:* data augmentation agents, teacher-student environments,
synthetic trajectory generation
*Key papers:* *(to be filled)*

---

## Cross-Thread Patterns

*(Filled in as patterns emerge across ingested papers.)*

Examples of patterns we expect to see:

- **"Verifiable vs. non-verifiable rewards"** — the dividing line that
  organizes recent RL-for-LLM work.
- **"Single-turn training → multi-turn deployment"** — a recurring shortcut
  in agent training.
- **"Self-play / self-evolution"** — a family of methods that keep appearing
  across multi-agent reasoning, agent trajectory optimization, and test-time RL.

---

## Knowledge Map (ASCII sketch)

```
                     ┌────────────────┐
                     │     LLMs       │
                     └──────┬─────────┘
                            │
        ┌───────────────────┼────────────────────┐
        │                   │                    │
  ┌─────▼──────┐     ┌──────▼───────┐     ┌──────▼───────┐
  │  Alignment │     │   Agents     │     │ Multimodal   │
  │  (RLHF/DPO │     │ (ReAct, GUI, │     │  (VLM, video │
  │   GRPO…)   │     │  multi-agent)│     │   grounding) │
  └─────┬──────┘     └──────┬───────┘     └──────┬───────┘
        │                   │                    │
        └────────┬──────────┘                    │
                 │                               │
        ┌────────▼─────────┐             ┌───────▼──────┐
        │ Reward Modeling  │             │     AIGC     │
        │ (process RM,     │             │ (diffusion,  │
        │  long-horizon RM)│             │  watermark)  │
        └──────────────────┘             └──────────────┘
```

---

## How to Use This Page

- **As a reader:** skim the threads to locate your area of interest,
  then jump to the linked papers and concepts.
- **As the LLM ingest agent:** after ingest, ask yourself
  *"does this paper change the shape of any thread above?"*
  If yes, edit this file. If no, just update `index.md`.
- **As the human curator:** occasionally re-read this page end-to-end
  and prune outdated narrative.

---

## See Also

- [`index.md`](./index.md) — the full content catalog
- [`../CLAUDE.md`](../CLAUDE.md) — schema and operating rules
