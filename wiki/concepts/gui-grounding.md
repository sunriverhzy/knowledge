---
title: "GUI Grounding"
aliases: ["Visual Grounding for GUI", "UI Grounding", "GUI Element Localization"]
domain: agent
tags: [gui-agent, grounding, multimodal, rl, visual-grounding]
source_count: 10
last_updated: 2026-05-20
---

# GUI Grounding

## Definition
**GUI Grounding** is the task of mapping a **natural-language instruction or high-level intent** to a precise **screen coordinate or UI element** on a graphical interface. It is the "last mile" of GUI automation: even if an agent plans the correct action sequence, failure to accurately localize `click(x, y)` can abort the entire task.

Formally, given screenshot $I \in \mathbb{R}^{H \times W \times 3}$ and query $q$ (e.g., "the submit button"), grounding produces coordinates:

$$
(x^*, y^*) = f_\theta(I, q), \quad \text{success} \iff (x^*, y^*) \in \text{BBox}(e)
$$

where $e$ is the target UI element with ground-truth bounding box $\text{BBox}(e)$.

## Intuition
Grounding is a **precision task**, not a reasoning task. A model with excellent high-level planning can still fail if it clicks 3 pixels outside a small button. This motivates dedicated grounding models and dense reward functions (IoU, distance-based) separate from the task-level sparse reward.

An important finding from [[liu-2026-infigui-g1]]: **adding Chain-of-Thought reasoning *hurts* grounding precision** because it introduces hallucination during coordinate prediction. Grounding is a "System 1" task; planning is a "System 2" task. See [[multi-turn-gui-rl#system-1-vs-system-2]].

## Historical Context
- **2018** — *Mapping NL to Web Elements* (Pasupat et al.): early work on grounding web elements from text queries.
- **2024** — **SeeClick** and **ScreenSpot** establish IoU-based grounding as a standalone benchmark. **OS-ATLAS** scales to 13M grounding examples.
- **2025** — **ScreenSpot-Pro** targets high-resolution professional screens; grounding becomes critical for production-quality agents. **GUI-G2** introduces Gaussian reward to solve "1-pixel deviation = failure". **UI-AGILE**, **SE-GUI**, **InfiGUI-G1**, **GUI-Eyes** push RL-based grounding.

## Key Variants / Subtypes

- **Coordinate regression**: directly predict `(x, y)` or normalized coordinates. Used by UI-TARS, OS-ATLAS, SeeClick.
- **Element-reference grounding**: identify an accessibility-tree node, then use its bounding box center.
- **Fast-thinking (System 1) grounding**: suppress reasoning; directly regress coordinates. [[liu-2026-infigui-g1]].
- **Active perception grounding**: invoke visual zoom / crop tools before predicting coordinates. [[chen-2026-gui-eyes]].
- **Reward-shaped RL grounding**: use continuous IoU or Gaussian rewards. [[tang-2025-gui-g2]], [[lian-2025-ui-agile]].
- **Self-evolutionary grounding**: use attention maps as pseudo-labels. [[yuan-2025-se-gui]].

## Technical Details

### Grounding benchmarks

| Benchmark | Platform | Metric | Key challenge |
|-----------|----------|--------|---------------|
| ScreenSpot | Cross-platform | Accuracy (@IoU 0.5) | General element localization |
| ScreenSpot-Pro | Desktop (Pro) | Accuracy (@IoU 0.5) | High-resolution small elements |
| Mind2Web-grounding | Web | Accuracy | Web-specific element types |
| AndroidWorld-grounding | Mobile | Accuracy | Mobile gesture areas |

### Reward functions for RL grounding

- **Binary hit/miss**: $r = 1$ if $(x^*, y^*) \in \text{BBox}(e)$, else $0$. Simple but sparse.
- **Distance-based** (UI-AGILE): $r = \max\left(0, 1 - \frac{\text{dist}((x^*, y^*), \text{center}(e))}{\text{threshold}}\right)$. Dense gradient.
- **IoU-based**: $r = \text{IoU}(\hat{B}, \text{BBox}(e))$ for box prediction.
- **Gaussian** (GUI-G2): $r = \exp\left(-\frac{\|(x^*, y^*) - \mu_e\|^2}{2\sigma^2}\right)$. Handles the "1-pixel failure" problem.

## Key Papers

| Paper | Year | Contribution |
|-------|------|--------------|
| [[li-2024-seeclick]] | 2024 | SeeClick: GUI grounding as core VLM capability; ScreenSpot benchmark. |
| [[you-2024-ferret-ui]] | 2024 | Ferret-UI: mobile grounding with any-resolution adaptability; visual CoT. |
| [[wu-2024-os-atlas]] | 2024 | OS-ATLAS: 13M grounding examples; strong baseline for GUI action model. |
| [[liu-2026-infigui-g1]] | 2026 | AEPO for grounding; CoT hurts precision; fast-thinking templates. |
| [[tang-2025-gui-g2]] | 2025 | Gaussian reward modeling; solves "1-pixel = failure" problem. |
| [[lian-2025-ui-agile]] | 2025 | Dense IoU rewards + cropping-based resampling; 23% gain on ScreenSpot. |
| [[chen-2026-gui-eyes]] | 2026 | Active perception: invoke zoom tools before grounding. |
| [[yuan-2025-se-gui]] | 2025 | Self-evolutionary RL: attention-map pseudo-labels for grounding. |

## Comparison with Related Concepts

|                    | GUI Grounding                         | GUI Planning                           |
|--------------------|---------------------------------------|----------------------------------------|
| Task type          | System 1 (fast, precise)              | System 2 (slow, deliberative)          |
| Output             | `(x, y)` coordinate                   | Action sequence / subgoal plan         |
| Does CoT help?     | Often **no** (hallucination risk)      | Generally **yes**                      |
| Reward signal      | Dense (IoU / distance-based)          | Sparse (task completion)               |
| RL algorithm       | GRPO / RLVR / continuous reward RL    | GRPO / hierarchical RL                 |

## Current Research Frontiers
- **High-resolution production screens**: ScreenSpot-Pro shows current VLMs still struggle on 4K / Retina displays.
- **Active perception**: query the environment for zoom patches rather than grinding pixel resolution.
- **Dynamic element grounding**: elements that move, appear, or disappear between steps (loading spinners, dropdowns).
- **Coordinate-free grounding**: semantic element reference rather than pixel coordinates. See GUI-Actor.

## See Also
- [[gui-agent]]
- [[computer-use-agent]]
- [[reinforcement-learning-with-verifiable-rewards]]
- [[multi-turn-gui-rl]]
- [[api-gui-hybrid-action-space]]
