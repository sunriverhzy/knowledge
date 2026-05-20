---
title: "Chain of Thought"
aliases: ["CoT", "Chain-of-Thought Reasoning", "Long CoT"]
domain: llm
tags: [chain-of-thought, prompt-engineering, reasoning, llm, test-time]
source_count: 1
last_updated: 2026-04-27
---

# Chain of Thought

## Definition
**Chain-of-Thought (CoT)** is the pattern in which a language model
produces an explicit, step-by-step **reasoning trace** before emitting
a final answer. CoT can arise from prompting (*"Let's think step by
step"*), from supervised fine-tuning on reasoning traces, or from
reinforcement learning objectives that reward correct-at-the-end
rollouts.

## Intuition
Pretraining teaches LLMs to emit *plausible-sounding* tokens. Without
an explicit reasoning trace, complex problems collapse into a single
forward pass — too little test-time compute to resolve them. CoT
**allocates more forward passes** (tokens) to the hard ones, letting
intermediate computations materialize in the context window where
they can be re-attended.

## Historical Context
- **2022** — Wei et al., *Chain-of-thought prompting elicits reasoning
  in large language models* — demonstrates that adding reasoning
  exemplars in a few-shot prompt unlocks multi-step math / logic.
- **2022** — Wang et al., *Self-Consistency* — sample multiple CoTs,
  majority-vote the final answer; the template for "vertical"
  test-time scaling.
- **2023** — Yao et al., *Tree-of-Thoughts* — structured search over
  reasoning trees.
- **2024** — OpenAI o1 / o3: **long-horizon CoT** trained via RL,
  pushing single reasoning traces from ~100 tokens to thousands.
- **2025** — DeepSeek-R1 (Guo et al.) publicly demonstrates that
  long-CoT reasoning can be elicited via rule-based RL with GRPO;
  QwQ-32B, Grok-3, and subsequent models follow. Long-CoT becomes the
  default for frontier models.

## Key Variants / Subtypes

- **Short CoT (prompted)** — a few exemplars in-context; reasoning
  traces of ~100–500 tokens. The classical Wei et al. regime.
- **Self-Consistency / vertical scaling** — sample $m$ CoTs, aggregate
  answers. Improves reliability, bounded by shallow-reasoning ceiling.
- **Structured CoT (Tree-of-Thoughts, Graph-of-Thoughts)** —
  branching / searching over intermediate states.
- **Long-horizon / Long CoT** — single traces of 1k–10k+ tokens, often
  with self-reflection, backtracking, hypothetical reasoning. Elicited
  by SFT on long traces + RL (DeepSeek-R1, o1). The style exploited by
  [[hong-2025-think-rm]] *for reward modeling* rather than the usual
  task policy.
- **CoT as reward / verifier signal** — use reasoning traces to emit
  either a final judgment ([[generative-reward-model]]s) or per-step
  process rewards (process reward models).

## Technical Details

### Horizontal vs. vertical inference-time scaling
Two orthogonal axes for spending more compute at inference:

- **Vertical scaling**: sample $m$ independent short CoTs, aggregate
  via majority vote or averaging. Total tokens: $m \times L_\text{short}$.
- **Horizontal scaling**: one trajectory of length $L_\text{long}$ with
  self-reflection and branching inside the chain. Total tokens:
  $L_\text{long}$.

Empirically (e.g. [[hong-2025-think-rm]]), horizontal scaling dominates
on tasks requiring **deep, coherent reasoning** (code, math, long
multi-turn); vertical dominates when errors are unbiased noise the
majority vote can filter.

### Training long CoT
Two common recipes:
1. **SFT warm-up** on long CoT data (synthesize with a frontier reasoner
   like o1 / QwQ, select longest-correct traces), then **rule-based RL**
   with verifiable rewards (DeepSeek-R1; [[hong-2025-think-rm]] applies
   this recipe to *reward modeling* rather than the downstream task).
2. **Pure RL from base LLM** — possible but less data-efficient than
   the SFT+RL recipe.

## Key Papers

| Paper | Year | Contribution |
|-------|------|--------------|
| [[hong-2025-think-rm]] | 2025 | Applies long-horizon CoT (via SFT+RL) to *reward modeling*, enabling self-reflective judgments. |

*(Foundational papers to ingest: Wei 2022 — CoT prompting;
Wang 2022 — Self-Consistency; Yao 2023 — Tree of Thoughts;
Guo 2025 — DeepSeek-R1.)*

## Comparison with Related Concepts

|                     | Short CoT (prompted) | Long CoT (RL-trained)   | Tree-of-Thoughts        |
|---------------------|----------------------|-------------------------|-------------------------|
| Traces per answer   | 1 (or small $m$)      | 1                       | many (tree nodes)       |
| Tokens per trace    | 100–500              | 1k–10k+                 | small per node          |
| Training needed?    | No                   | Yes (SFT + RL)          | Usually inference-only  |
| Self-reflection?    | Rare                 | Common                  | External search control |
| Best for            | Simple multi-step    | Complex, subtle reasoning | Combinatorial search  |

## Current Research Frontiers
- **Horizontal vs. vertical trade-off curves** — what is the Pareto
  frontier as we increase model size?
- **Length bias in CoT evaluation** — long chains correlate with
  correctness, but also inflate cost; how to score efficiency jointly.
- **CoT for judges, not just solvers** — [[hong-2025-think-rm]] shows
  CoT benefits evaluation, not only generation.
- **Faithfulness of CoT** — does the trace reflect the model's true
  computation, or is it post-hoc?

## See Also
- [[generative-reward-model]]
- [[reinforcement-learning-from-human-feedback]]
- [[group-relative-policy-optimization]]
