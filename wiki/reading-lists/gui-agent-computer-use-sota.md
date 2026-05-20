---
title: "GUI Agent / Computer Use SOTA Survey Note"
domain: [agent, multimodal, rl]
difficulty: advanced
last_updated: 2026-05-20
source_review: "arXiv:2604.27955"
raw_manifest: "../../raw/gui_agent_computer_use_sota_download_manifest.txt"
---

# GUI Agent / Computer Use SOTA Survey Note

> Scope: this note summarizes the GUI Agent / Computer Use literature around **frontier models, RL methods, benchmarks, grounding, mobile, desktop, and web agents**, based primarily on *GUI Agents with Reinforcement Learning: Toward Digital Inhabitants* (`arXiv:2604.27955`) plus the downloaded paper set in `raw/gui_agent_computer_use_sota_download_manifest.txt`.
>
> Date: 2026-05-20. The field moves quickly; treat this as a snapshot rather than a permanent leaderboard.

---

## Executive Summary

The current SOTA trend is no longer "prompt a VLM to click buttons". The field is moving toward **RL-trained, environment-grounded, multi-turn computer-use agents**. The best systems combine:

1. **Native screenshot-based perception** rather than brittle DOM / accessibility-only pipelines.
2. **RL fine-tuning** — especially [[group-relative-policy-optimization]] and RL with verifiable rewards (RLVR).
3. **Hybrid offline-to-online training**: SFT / BC for safe initialization, offline preference or value learning for scale, and online / semi-online RL for recovery and distribution correction.
4. **Cross-platform action spaces** spanning desktop, mobile, and web.
5. **Dense reward interfaces**: rule-based checks, IoU grounding rewards, outcome-supervised reward models, model-as-judge reward signals, and process / progress rewards.
6. **Infrastructure-level scaling**: browser / VM / emulator parallelism, asynchronous rollouts, and increasingly world-model or simulator-assisted training to overcome the GUI I/O wall.

A useful mental model:

```text
SOTA GUI agent = VLM backbone + action model + grounding module + memory/planning + RLVR/GRPO + environment verifier + rollout infrastructure
```

The strongest research direction is **hybrid RL for verifiable computer use**: use offline data to learn safe UI semantics, then use online / semi-online interaction to learn recovery, efficiency, and long-horizon planning.

---

## 1. Frontier Models

### 1.1 Closed-source frontier systems

| System | Source | Main idea | Notes |
|---|---|---|---|
| OpenAI Computer-Using Agent / Operator | OpenAI website, no PDF | RL-enhanced autonomous computer control with verifiable outcomes | Important commercial baseline; no arXiv PDF in `raw/`. |
| Claude Computer Use | Anthropic website, no PDF | Pure screenshot-based computer-use tool calling with safety-oriented RLHF / Constitutional AI lineage | Strong example of visual-only computer control. |
| Gemini 2.5 Computer Use | Google blog / model release | Commercial computer-use model for browser / app interaction | No paper PDF found. |

These systems matter because they validate the product direction, but they are hard to study scientifically because training data, reward design, and benchmark results are not fully disclosed.

### 1.2 Open-source / paper-backed frontier systems

| Model / Paper | Category | Core contribution | Downloaded PDF |
|---|---|---|---|
| `UI-TARS` (`2501.12326`) | General-purpose GUI agent | Native screenshot-only GUI agent, cross-platform, "thoughts-before-actions", strong benchmark coverage | `raw/2501.12326 - UI-TARS - Pioneering Automated GUI Interaction with Native Agents.pdf` |
| `UI-TARS-2` (`2509.02544`) | Multi-turn RL GUI agent | Advances UI-TARS with multi-turn RL and stronger computer-use capabilities | `raw/2509.02544 - UI-TARS-2 Technical Report - Advancing GUI Agent with Multi-turn Reinforcement Learning.pdf` |
| `OpenCUA` (`2508.09123`) | Open foundation for computer-use agents | Open data / model / training foundations for computer-use agents | `raw/2508.09123 - OpenCUA - Open Foundations for Computer-use Agents.pdf` |
| `ComputerRL` (`2508.14040`) | End-to-end online RL for computer use | API-GUI hybrid action space; tackles entropy collapse and I/O-bound online RL | `raw/2508.14040 - ComputerRL - Scaling End-to-end Online Reinforcement Learning for Computer Use Agents.pdf` |
| `Agent S` (`2410.08164`) | Open computer-use framework | Framework for human-like computer use | `raw/2410.08164 - Agent S - An Open Agentic Framework That Uses Computers Like a Human.pdf` |
| `Agent S2` (`2504.00906`) | Generalist-specialist computer-use agent | Compositional generalist-specialist architecture | `raw/2504.00906 - Agent S2 - A Compositional Generalist-specialist Framework for Computer Use Agents.pdf` |
| `OS-ATLAS` (`2410.23218`) | Foundation action model | Generalist action model for GUI agents | `raw/2410.23218 - OS-ATLAS - A Foundation Action Model for Generalist GUI Agents.pdf` |
| `Aguvis` (`2412.04454`) | Pure vision GUI agent | Unified pure-vision agents for autonomous GUI interaction | `raw/2412.04454 - Aguvis - Unified Pure Vision Agents for Autonomous GUI Interaction.pdf` |
| `ShowUI` (`2411.17465`) | Vision-language-action model | One VLA model for GUI visual agents | `raw/2411.17465 - ShowUI - One Vision-language-action Model for GUI Visual Agent.pdf` |
| `Falcon-UI` (`2412.09362`) | GUI pretraining | Understand GUI before following instructions; emphasizes GUI representation learning | `raw/2412.09362 - Falcon-UI - Understanding GUI Before Following User Instructions.pdf` |
| `InfiGUIAgent` (`2501.04575`) | Reasoning / reflection | Multimodal generalist GUI agent with native reasoning and reflection | `raw/2501.04575 - InfiGUIAgent - A Multimodal Generalist GUI Agent with Native Reasoning and Reflection.pdf` |
| `UFO2` (`2504.14603`) | Desktop AgentOS | Desktop agent operating-system layer | `raw/2504.14603 - UFO2 - The Desktop AgentOS.pdf` |
| `UltraCUA` (`2510.17790`) | Foundation CUA with hybrid action | Combines visual primitives and API/tool actions for broader computer use | `raw/2510.17790 - UltraCUA - A Foundation Model for Computer Use Agents with Hybrid Action.pdf` |

### 1.3 Frontier-model takeaway

The frontier is bifurcating:

- **Native visual agents**: UI-TARS, Aguvis, ShowUI, Falcon-UI, InfiGUIAgent. These prioritize cross-platform generality.
- **Hybrid action computer-use agents**: ComputerRL, UltraCUA, UFO2, OS-Copilot. These combine low-level GUI control with higher-level APIs/tools to reduce trajectory length and improve reliability.

The likely winning architecture is not pure GUI or pure API, but **API-GUI hybrid**: use GUI when APIs do not exist; use APIs when they are safer and shorter.

---

## 2. RL Methods

### 2.1 Offline RL / Offline RFT

Offline methods are the safety layer. They let the agent learn basic UI semantics before touching live systems.

| Method family | Representative papers | What it solves | Weakness |
|---|---|---|---|
| Behavioral cloning / SFT | Android in the Wild, UI-TARS data | Safe initialization | Compounding errors under distribution shift |
| Value-based offline RL | `Digi-Q` | Learns action values from static device-control data; supports Best-of-N reranking | Needs value estimation; risk of OOD action evaluation |
| Preference optimization | `UI-TARS`, `Agent Q`, ARPO | Uses successful vs failed trajectories; stable for large VLMs | Requires high-quality preference pairs / search data |
| Offline GRPO / RLVR | `GUI-R1`, `UI-R1` | Uses verifiable rewards without a learned critic | Works best when rewards are cleanly verifiable |

Key papers:

- `Digi-Q` (`2502.15760`): frozen VLM backbone + Q-value head + Best-of-N action reranking.
- `UI-TARS` (`2501.12326`): large-scale cross-platform agent with SFT + RL-style improvement.
- `Agent Q` (`2408.07199`): "search is data"; MCTS discovers high-quality web trajectories for DPO.
- `GUI-R1` (`2504.10458`) and `UI-R1` (`2503.21620`): rule-based RLVR / GRPO with unified GUI action spaces.

### 2.2 Online RL

Online RL is needed for recovery, adaptation, and long-horizon credit assignment, but it is limited by GUI I/O latency.

| Method | Core idea | Why it matters |
|---|---|---|
| `WebRL` (`2411.02337`) | Self-evolving online curriculum for web agents | Curriculum learning replaces random exploration. |
| `MobileRL` (`2509.18119`) | AdaGRPO + difficulty weighting for mobile GUI | Handles heavy-tailed task difficulty and suppresses redundant operations. |
| `WebAgent-R1` (`2505.16421`) | Multi-turn GRPO over full web trajectories | Optimizes whole trajectories rather than single-step actions. |
| `ComputerRL` (`2508.14040`) | End-to-end online RL for computer use | Treats computer-use training as a systems + RL problem. |

Online RL's bottleneck is not algorithm maturity alone; it is the **I/O wall**: screenshots, rendering, DOM parsing, network requests, emulator or VM latency. This is why pure online RL is rarely enough.

### 2.3 Hybrid RL: the current dominant paradigm

Hybrid methods combine safe initialization with adaptive interaction:

```text
SFT / BC → offline RL or preference learning → semi-online / online RL → verifier-guided self-improvement
```

Representative systems:

| System | Hybrid pattern | Key idea |
|---|---|---|
| `DigiRL` (`2406.11896`) | Offline-to-online | Initialize from Android in the Wild, then online fine-tune with VLM evaluator. |
| `UI-S1` (`2509.11543`) | Semi-online RL | Uses trajectory patching to mimic online correction over static data. |
| `MANO` (`2509.17336`) | SFT → Offline RL → Online RL | Three-stage GRPO with composite rewards and closed-loop data cycle. |
| `DynaWeb` (`2601.22149`) | Model-based RL | Uses a web world model to dream rollouts, reducing live interaction cost. |
| `Hi-Agent` (`2510.14388`) | Hierarchical RL | Planner + executor; trains subgoal-level and UI-action-level policies. |
| `UltraCUA` (`2510.17790`) | Hybrid action space | Routes between GUI actions and APIs/tools. |

### 2.4 RL-method takeaway

The dominant algorithmic stack is:

- **DPO / preference optimization** for offline trajectory comparison.
- **GRPO / RLVR** for verifiable GUI rewards and memory-efficient VLM RL.
- **Curriculum learning** for cold-start and long-tail tasks.
- **World models / synthetic rollouts** for escaping the GUI I/O wall.
- **Hierarchical RL** for long-horizon credit assignment.

This directly connects to existing wiki concepts: [[group-relative-policy-optimization]], [[reinforcement-learning-from-human-feedback]], [[reward-hacking]], and [[chain-of-thought]].

---

## 3. Benchmarks / Environments

Benchmarks are not merely evaluation tools; in GUI RL they are **training environments** and **reward providers**.

### 3.1 Cross-platform / desktop / OS

| Benchmark / Environment | Platform | Role | PDF |
|---|---|---|---|
| `OSWorld` (`2404.07972`) | Desktop / OS | Real-computer environment with 369 open-ended tasks; execution-based evaluation | `raw/2404.07972 - OSWorld - Benchmarking Multimodal Agents for Open-ended Tasks in Real Computer Environments.pdf` |
| `Windows Agent Arena` (`2409.08264`) | Windows | Multi-modal OS-agent evaluation at scale | `raw/2409.08264 - Windows Agent Arena - Evaluating Multi-modal OS Agents at Scale.pdf` |
| `OmniAct` (`2402.17553`) | Desktop + web | Dataset / benchmark for multimodal generalist autonomous agents | `raw/2402.17553 - OmniAct - A Dataset and Benchmark for Enabling Multimodal Generalist Autonomous Agents.pdf` |
| `ScreenAgent` (`2402.07945`) | Desktop | VNC / pixel-stream computer-control agent | `raw/2402.07945 - ScreenAgent - A Vision Language Model-driven Computer Control Agent.pdf` |

### 3.2 Web / browser

| Benchmark / Environment | Role | PDF |
|---|---|---|
| `WebArena` (`2307.13854`) | Self-hosted realistic web environment with executable verification | `raw/2307.13854 - WebArena - A Realistic Web Environment for Building Autonomous Agents.pdf` |
| `VisualWebArena` (`2401.13649`) | Adds visual web tasks; multimodal web-agent benchmark | `raw/2401.13649 - VisualWebArena - Evaluating Multimodal Agents on Realistic Visual Web Tasks.pdf` |
| `BrowserGym` (`2412.05467`) | Unified API / ecosystem for WebArena, MiniWoB++, VisualWebArena, WebChoreArena | `raw/2412.05467 - BrowserGym - The BrowserGym Ecosystem for Web Agent Research.pdf` |
| `Mind2Web` (`2306.06070`) | Web demonstration / trajectory dataset; semantic DOM filtering | `raw/2306.06070 - Mind2Web - Towards a Generalist Agent for the Web.pdf` |
| `WebVoyager` (`2401.13919`) | End-to-end multimodal web agent baseline | `raw/2401.13919 - WebVoyager - Building an End-to-end Web Agent with Large Multimodal Models.pdf` |

### 3.3 Mobile

| Benchmark / Environment | Role | PDF |
|---|---|---|
| `Android in the Wild` (`2307.10088`) | Large-scale Android device-control dataset | `raw/2307.10088 - Android in the Wild - A Large-scale Dataset for Android Device Control.pdf` |
| `AndroidWorld` (`2405.14573`) | Dynamic mobile RL environment with parameterized tasks and verifiable rewards | `raw/2405.14573 - AndroidWorld - A Dynamic Benchmarking Environment for Autonomous Agents.pdf` |

### 3.4 Benchmark takeaway

The best benchmarks share three properties:

1. **Executable verification**: inspect real side effects, not textual self-reports.
2. **Resettable environments**: required for online RL and repeated rollout.
3. **Diverse task families**: prevent rote memorization and enable curriculum learning.

`OSWorld`, `WebArena`, `VisualWebArena`, and `AndroidWorld` are therefore the core environment quartet for current GUI / computer-use research.

---

## 4. Grounding / Perception

Grounding is the local-control bottleneck: even a good planner fails if a click misses by a few pixels.

### 4.1 Core grounding papers and datasets

| Paper / Dataset | Main contribution | PDF |
|---|---|---|
| `SeeClick` (`2401.10935`) | Harnesses GUI grounding for advanced visual GUI agents | `raw/2401.10935 - SeeClick - Harnessing GUI Grounding for Advanced Visual GUI Agents.pdf` |
| `Ferret-UI` (`2404.05719`) | Grounded mobile UI understanding with MLLMs | `raw/2404.05719 - Ferret-UI - Grounded Mobile UI Understanding with Multimodal LLMs.pdf` |
| `Ferret-UI 2` (`2410.18967`) | Universal UI understanding across platforms | `raw/2410.18967 - Ferret-UI 2 - Mastering Universal User Interface Understanding Across Platforms.pdf` |
| `ScreenSpot-Pro` (`2504.07981`) | Professional high-resolution computer-use grounding benchmark | `raw/2504.07981 - ScreenSpot-Pro - GUI Grounding for Professional High-resolution Computer Use.pdf` |
| `OS-ATLAS` (`2410.23218`) | Foundation action model; action-grounding at scale | `raw/2410.23218 - OS-ATLAS - A Foundation Action Model for Generalist GUI Agents.pdf` |
| `GUI-G2` (`2507.15846`) | Gaussian reward modeling for GUI grounding | `raw/2507.15846 - GUI-G2 - Gaussian Reward Modeling for GUI Grounding.pdf` |
| `UI-AGILE` (`2507.22025`) | Dense distance / IoU-style grounding rewards + inference-time grounding | `raw/2507.22025 - UI-AGILE - Advancing GUI Agents with Effective Reinforcement Learning and Precise Inference-time Grounding.pdf` |
| `InfiGUI-G1` (`2508.05731`) | Adaptive Exploration Policy Optimization for grounding | `raw/2508.05731 - InfiGUI-G1 - Advancing GUI Grounding with Adaptive Exploration Policy Optimization.pdf` |
| `GUI-Eyes` (`2601.09770`) | Tool-augmented active perception before grounding | `raw/2601.09770 - GUI-Eyes - Tool-augmented Perception for Visual Grounding in GUI Agents.pdf` |
| `SE-GUI` (`2505.12370`) | Self-evolutionary RL via attention-based pseudo-labels | `raw/2505.12370 - SE-GUI - Enhancing Visual Grounding for GUI Agents via Self-evolutionary Reinforcement Learning.pdf` |

### 4.2 Key grounding insight

For grounding, **more reasoning is not always better**. The review notes that `InfiGUI-G1` finds explicit CoT can reduce coordinate precision by introducing hallucination. This suggests a dual-system architecture:

- **System 1**: fast, direct coordinate grounding for local actions.
- **System 2**: slower reasoning for planning, recovery, and multi-step task decomposition.

The open problem is learning when to route between the two.

---

## 5. Mobile Agents

Mobile GUI is a major SOTA battleground because Android provides strong instrumentation (`ADB`, SQLite state inspection, emulator scaling) while still requiring visual / gesture-based interaction.

### 5.1 Core mobile papers

| Paper | Role | PDF |
|---|---|---|
| `Android in the Wild` (`2307.10088`) | Large-scale real-world Android traces | `raw/2307.10088 - Android in the Wild - A Large-scale Dataset for Android Device Control.pdf` |
| `AndroidWorld` (`2405.14573`) | Dynamic mobile benchmark / RL environment | `raw/2405.14573 - AndroidWorld - A Dynamic Benchmarking Environment for Autonomous Agents.pdf` |
| `DigiRL` (`2406.11896`) | Offline-to-online RL for device-control agents | `raw/2406.11896 - DigiRL - Training In-the-wild Device-control Agents with Autonomous Reinforcement Learning.pdf` |
| `Digi-Q` (`2502.15760`) | Offline Q-value learning + Best-of-N reranking | `raw/2502.15760 - Digi-Q - Learning Q-value Functions for Training Device-control Agents.pdf` |
| `Mobile-Agent-v3` (`2508.15144`) | Fundamental mobile / GUI automation agent | `raw/2508.15144 - Mobile-Agent-v3 - Fundamental Agents for GUI Automation.pdf` |
| `AgentCPM-GUI` (`2506.01391`) | Mobile-use agents with reinforcement fine-tuning | `raw/2506.01391 - AgentCPM-GUI - Building Mobile-use Agents with Reinforcement Fine-tuning.pdf` |
| `MagicGUI` (`2508.03700`) | Foundational mobile GUI agent + scalable data pipeline + RFT | `raw/2508.03700 - MagicGUI - A Foundational Mobile GUI Agent with Scalable Data Pipeline and Reinforcement Fine-tuning.pdf` |
| `MobileRL` (`2509.18119`) | Online agentic RL for mobile GUI agents | `raw/2509.18119 - MobileRL - Online Agentic Reinforcement Learning for Mobile GUI Agents.pdf` |
| `Hi-Agent` (`2510.14388`) | Hierarchical VLA agents for mobile device control | `raw/2510.14388 - Hi-Agent - Hierarchical Vision-language Agents for Mobile Device Control.pdf` |

### 5.2 Mobile takeaway

Mobile agents are where **offline-to-online RL** is most mature:

- `Android in the Wild` supplies demonstrations.
- `AndroidWorld` supplies resettable interactive tasks with ground-truth checks.
- `DigiRL` / `Digi-Q` demonstrate offline-to-online and offline value-learning patterns.
- `MobileRL` and `Hi-Agent` move toward difficulty-aware and hierarchical online RL.

The next frontier is reducing emulator cost and improving safe exploration under real user data constraints.

---

## 6. Desktop / OS Agents

Desktop and OS agents require cross-application workflows, file-system state tracking, window management, and multi-app memory. This is closer to real "computer use" than single-app mobile or single-site web tasks.

### 6.1 Core desktop / OS papers

| Paper | Role | PDF |
|---|---|---|
| `OSWorld` (`2404.07972`) | Core desktop / real-computer benchmark | `raw/2404.07972 - OSWorld - Benchmarking Multimodal Agents for Open-ended Tasks in Real Computer Environments.pdf` |
| `Windows Agent Arena` (`2409.08264`) | Scalable Windows OS-agent benchmark | `raw/2409.08264 - Windows Agent Arena - Evaluating Multi-modal OS Agents at Scale.pdf` |
| `ScreenAgent` (`2402.07945`) | VNC-based pixel-stream computer-control agent | `raw/2402.07945 - ScreenAgent - A Vision Language Model-driven Computer Control Agent.pdf` |
| `OS-Copilot` (`2402.07456`) | Generalist computer agents with self-improvement | `raw/2402.07456 - OS-Copilot - Towards Generalist Computer Agents with Self-improvement.pdf` |
| `UFO2` (`2504.14603`) | Desktop AgentOS | `raw/2504.14603 - UFO2 - The Desktop AgentOS.pdf` |
| `ComputerRL` (`2508.14040`) | End-to-end online RL for computer-use agents | `raw/2508.14040 - ComputerRL - Scaling End-to-end Online Reinforcement Learning for Computer Use Agents.pdf` |
| `Ponder & Press` (`2412.01268`) | Visual GUI agent toward general computer control | `raw/2412.01268 - Ponder and Press - Advancing Visual GUI Agent Towards General Computer Control.pdf` |
| `OpenCUA` (`2508.09123`) | Open foundations for computer-use agents | `raw/2508.09123 - OpenCUA - Open Foundations for Computer-use Agents.pdf` |

### 6.2 Desktop takeaway

Desktop agents expose the hardest version of computer use:

- state is distributed across files, apps, windows, and sometimes websites;
- evaluation needs execution-based side-effect inspection;
- trajectories are long and error recovery is essential;
- API-GUI hybrid action spaces are increasingly necessary.

`OSWorld` is the canonical benchmark, while `ComputerRL`, `OpenCUA`, and `UFO2` point toward the next generation of OS-level agents.

---

## 7. Web Agents

Web agents remain the most mature environment class because browser state is relatively instrumentable and resettable.

### 7.1 Core web papers

| Paper | Role | PDF |
|---|---|---|
| `Mind2Web` (`2306.06070`) | Generalist web-agent dataset; deterministic replay and DOM action compression | `raw/2306.06070 - Mind2Web - Towards a Generalist Agent for the Web.pdf` |
| `WebArena` (`2307.13854`) | Realistic self-hosted web benchmark | `raw/2307.13854 - WebArena - A Realistic Web Environment for Building Autonomous Agents.pdf` |
| `VisualWebArena` (`2401.13649`) | Multimodal web benchmark requiring visual perception | `raw/2401.13649 - VisualWebArena - Evaluating Multimodal Agents on Realistic Visual Web Tasks.pdf` |
| `WebVoyager` (`2401.13919`) | End-to-end web agent with large multimodal models | `raw/2401.13919 - WebVoyager - Building an End-to-end Web Agent with Large Multimodal Models.pdf` |
| `BrowserGym` (`2412.05467`) | Unified benchmark / environment API | `raw/2412.05467 - BrowserGym - The BrowserGym Ecosystem for Web Agent Research.pdf` |
| `Agent Q` (`2408.07199`) | MCTS + DPO for autonomous web-agent learning | `raw/2408.07199 - Agent Q - Advanced Reasoning and Learning for Autonomous AI Agents.pdf` |
| `WebRL` (`2411.02337`) | Self-evolving online curriculum RL for web agents | `raw/2411.02337 - WebRL - Training LLM Web Agents via Self-evolving Online Curriculum Reinforcement Learning.pdf` |
| `WebAgent-R1` (`2505.16421`) | End-to-end multi-turn RL for web agents | `raw/2505.16421 - WebAgent-R1 - Training Web Agents via End-to-end Multi-turn Reinforcement Learning.pdf` |
| `DynaWeb` (`2601.22149`) | Model-based RL using web world models | `raw/2601.22149 - DynaWeb - Model-based Reinforcement Learning of Web Agents.pdf` |

### 7.2 Web takeaway

Web agents are the best place to study **curriculum RL + executable verification + world models**. The path is:

```text
Mind2Web / WebArena demonstrations → WebRL curriculum → WebAgent-R1 multi-turn GRPO → DynaWeb world-model rollouts
```

BrowserGym is important because it standardizes fragmented web-agent environments into a common API.

---

## 8. Cross-Cutting SOTA Patterns

### Pattern A — Verifiable rewards are the center of gravity

GUI / computer-use tasks often expose objective state changes: URL transitions, DOM state, Android SQLite rows, filesystem changes, form submissions. This makes them ideal for RLVR compared with open-ended text generation.

### Pattern B — GRPO is becoming the default RL algorithm

For VLM-scale GUI agents, GRPO avoids a learned critic, reducing memory cost and simplifying training. It appears in `GUI-R1`, `UI-R1`, `MANO`, `InfiGUI-G1`, `GUI-Eyes`, and many newer systems.

### Pattern C — Hybrid beats pure offline or pure online

Pure offline suffers from distribution shift; pure online is expensive and unsafe. The practical SOTA is staged or hybrid:

```text
SFT → offline preference/value learning → semi-online correction → selective online RL → verifier-guided self-improvement
```

### Pattern D — Reasoning and grounding must be separated

Reasoning helps planning and recovery, but can hurt pixel-level coordinate precision. Future systems likely need learned routing between:

- fast visual grounding;
- slow deliberative planning;
- memory retrieval;
- verifier / judge calls;
- API vs GUI actions.

### Pattern E — Infrastructure is part of the algorithm

GUI RL is I/O-bound. Environment throughput, asynchronous rollout, browser / VM / emulator density, state compression, and reward verification pipelines are not implementation details; they determine what algorithms are feasible.

---

## 9. Recommended Reading Order

### Stage 1 — Foundations and benchmarks
1. `OSWorld` (`2404.07972`)
2. `WebArena` (`2307.13854`)
3. `VisualWebArena` (`2401.13649`)
4. `AndroidWorld` (`2405.14573`)
5. `Mind2Web` (`2306.06070`)

### Stage 2 — Native GUI / computer-use agents
1. `UI-TARS` (`2501.12326`)
2. `OpenCUA` (`2508.09123`)
3. `Agent S2` (`2504.00906`)
4. `OS-ATLAS` (`2410.23218`)
5. `Aguvis` (`2412.04454`)

### Stage 3 — RL methods
1. `DigiRL` (`2406.11896`)
2. `Digi-Q` (`2502.15760`)
3. `GUI-R1` (`2504.10458`)
4. `UI-R1` (`2503.21620`)
5. `WebRL` (`2411.02337`)
6. `WebAgent-R1` (`2505.16421`)
7. `ComputerRL` (`2508.14040`)
8. `DynaWeb` (`2601.22149`)

### Stage 4 — Grounding and perception
1. `SeeClick` (`2401.10935`)
2. `Ferret-UI` (`2404.05719`)
3. `ScreenSpot-Pro` (`2504.07981`)
4. `InfiGUI-G1` (`2508.05731`)
5. `GUI-Eyes` (`2601.09770`)

---

## 10. Open Problems

1. **Reliable reward interfaces**: how to combine rule checks, environment feedback, LLM-as-judge, and learned rewards without reward hacking?
2. **I/O-efficient RL**: how to scale online training when every GUI step costs 0.5–2 seconds?
3. **World-model fidelity**: how to use web / GUI world models without accumulating simulation-reality gap?
4. **Hierarchical control**: when should an agent think, click, call an API, ask for help, or backtrack?
5. **Cross-platform generalization**: can one policy span web, desktop, and mobile without overfitting to interface conventions?
6. **Safety and permissions**: how to train agents that can act in real computers without causing irreversible damage?
7. **Evaluation drift**: leaderboards change quickly; benchmarks need live, auditable, side-effect-aware evaluation.

---

## Bottom Line

The SOTA direction for GUI Agent / Computer Use is:

```text
native visual GUI understanding
+ verifiable environment feedback
+ GRPO / RLVR
+ hybrid offline-online training
+ API-GUI hybrid actions
+ asynchronous rollout infrastructure
+ memory / hierarchical planning
```

If you want to build a research wiki around this area, the first concept pages to create should be:

- `gui-agent`
- `computer-use-agent`
- `gui-grounding`
- `reinforcement-learning-with-verifiable-rewards`
- `api-gui-hybrid-action-space`
- `gui-io-wall`
- `multi-turn-gui-rl`
- `world-model-for-web-agents`
