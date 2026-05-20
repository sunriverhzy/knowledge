---
title: "GUI Agent"
aliases: ["GUI Agents", "Graphical User Interface Agent", "Screen Agent"]
domain: agent
tags: [agent, gui-agent, multi-agent, rl, multimodal, computer-use]
source_count: 20
last_updated: 2026-05-20
---

# GUI Agent

## Definition
A **GUI Agent** is an AI system that perceives **graphical user interfaces (GUIs)** through visual observations — primarily screenshots — and executes tasks by generating **human-like interactions**: mouse clicks, keyboard inputs, scrolling, and drag operations. Unlike CLI-based agents or API-based agents, GUI agents operate at the pixel / accessibility-tree level, enabling automation of any software a human can operate, regardless of whether a programmatic API exists.

Formally, a GUI agent can be modeled as a policy $\pi_\theta$ acting in a **Partially Observable Markov Decision Process (POMDP)**:

$$
(S, A, T, R, \gamma), \quad o_t = \text{screenshot}(s_t) \in \mathbb{R}^{H \times W \times 3}
$$

where the agent observes screenshots $o_t$, selects actions $a_t$ (e.g., `click(x, y)`, `type("text")`, `scroll(delta)`), and receives rewards $r_t$ derived from task completion or intermediate verification signals.

## Intuition
A GUI agent is essentially a robot that "sees the screen and uses the computer like a human" — without access to source code, APIs, or DOM trees in the general case. The core challenge is bridging **visual perception** (what is on screen?) to **action planning** (what should I do next?) to **grounding** (exactly where do I click?) — all under sparse, delayed rewards.

## Historical Context

| Period | Milestone |
|--------|-----------|
| 2017 | **MiniWob / MiniWob++** — first synthetic HTML5 benchmarks for web GUI RL. |
| 2021–2022 | **WebGPT**, **WebShop** — LLMs applied to web browsing and shopping tasks. |
| 2023 | **Mind2Web**, **WebArena** — realistic web-agent benchmarks; **Android in the Wild** — large-scale mobile data. |
| 2024 | **OSWorld**, **AndroidWorld** — real-computer / real-phone environments; **UI-TARS**, **OS-ATLAS** — native VLM-based GUI agents with RL training; commercial: **Claude Computer Use**, **OpenAI CUA**. |
| 2025–2026 | **GRPO / RLVR** as dominant GUI RL algorithm; **ComputerRL**, **DynaWeb**, **UI-TARS-2** — scaling RL for computer use; multi-turn and hierarchical agents. |

## Key Variants / Subtypes

- **Web Agent** — operates within browsers on websites. Environments: [[zhou-2023-webarena]], [[koh-2024-visualwebarena]], [[chezelles-2024-browsergym]].
- **Desktop / OS Agent** — operates on full operating systems with file I/O, multi-app workflows. Environments: [[xie-2024-osworld]], [[bonatti-2024-windows-agent-arena]].
- **Mobile Agent** — operates on smartphones via gesture-based touch interfaces. Environments: [[rawles-2024-androidworld]], [[rawles-2023-android-in-the-wild]].
- **Computer-Use Agent** — broader term covering cross-platform agents combining web, desktop, and sometimes mobile; see [[computer-use-agent]].

## Technical Details

### Observation space
- **Screenshot** (primary): $H \times W \times 3$ pixel array.
- **Accessibility tree** (optional): structured DOM or OS accessibility metadata for element names, bounding boxes, roles.
- **History**: previous screenshots, actions, and optionally chain-of-thought summaries.

### Action space
| Type | Example |
|------|---------|
| Click | `click(x, y, button)` |
| Type | `type("search query")` |
| Scroll | `scroll(x, y, delta)` |
| Key | `key("Enter")` |
| API (hybrid) | `get_file_content(path)` |

### Training paradigms
See [[reinforcement-learning-with-verifiable-rewards]], [[group-relative-policy-optimization]].

- **Offline RFT** (dominant entry point): SFT on human demonstrations + DPO / GRPO on trajectory preference pairs.
- **Online RL**: agent interacts with real or simulated environments; curriculum learning critical for cold-start.
- **Hybrid**: offline warm start → semi-online / online fine-tuning.

### Reward engineering
Three-tier pyramid:

1. **Rule-based**: coordinate-in-bounding-box, URL match, form submission, DOM/file state check.
2. **LLM-as-Judge**: model evaluates task completion from screenshots.
3. **Learned RM**: outcome-supervised reward model (ORM) or process reward model (PRM).

## Key Papers

| Paper | Year | Contribution |
|-------|------|--------------|
| [[xie-2024-osworld]] | 2024 | First real-computer OS benchmark with execution-based evaluation. |
| [[qin-2025-ui-tars]] | 2025 | Native screenshot-only GUI agent; cross-platform SFT+RL; SOTA on 10+ benchmarks. |
| [[bai-2024-digiirl]] | 2024 | Offline-to-online RL for device-control agents; VLM-as-evaluator. |
| [[luo-2025-gui-r1]] | 2025 | Rule-based RLVR with GRPO; 3K samples → emergent reasoning. |
| [[lai-2025-computerrl]] | 2025 | API-GUI hybrid action space; end-to-end online RL for computer use. |

## Comparison with Related Concepts

|                       | GUI Agent               | [[computer-use-agent]] | CLI Agent                 |
|-----------------------|-------------------------|------------------------|---------------------------|
| Input modality        | Screenshots (+ optional AT) | Screenshots + APIs     | Text / command output     |
| Output action space   | Pixel-level mouse/kb    | GUI + API / tools      | Shell commands            |
| Coverage              | Any software            | Any software + APIs    | API-accessible only       |
| RL difficulty         | High (visual, sparse)   | Medium (hybrid signal) | Lower (deterministic)     |
| Benchmark examples    | OSWorld, AndroidWorld   | ComputerRL, OpenCUA    | SWE-bench, InterCode      |

## Current Research Frontiers
- **Hybrid action spaces**: combining GUI pixel-level actions with high-level API / tool calls.
- **[[gui-grounding]]** as a standalone RL problem: precision grounding under sparse, high-dimensional visual state.
- **[[multi-turn-gui-rl]]**: multi-turn GRPO and credit assignment across long interaction trajectories.
- **[[gui-world-model]]**: model-based RL to escape the [[gui-io-wall]].
- **Hierarchical control**: routing between fast System-1 grounding and slow System-2 reasoning.
- **Safety and permission models**: how to train agents that can't accidentally delete files or make purchases.

## See Also
- [[computer-use-agent]]
- [[gui-grounding]]
- [[reinforcement-learning-with-verifiable-rewards]]
- [[multi-turn-gui-rl]]
- [[gui-io-wall]]
- [[api-gui-hybrid-action-space]]
- [[group-relative-policy-optimization]]
