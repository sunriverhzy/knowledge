---
title: "Computer-Use Agent"
aliases: ["CUA", "Computer Use Agent", "Computer-using Agent"]
domain: agent
tags: [agent, computer-use, gui-agent, rl, multimodal]
source_count: 8
last_updated: 2026-05-20
---

# Computer-Use Agent

## Definition
A **Computer-Use Agent (CUA)** is an AI system that can operate a real computer — including its operating system, installed applications, browser, and file system — to complete arbitrary user-specified tasks. Unlike narrow GUI agents targeting a single platform (mobile or web), a CUA is expected to handle **cross-platform**, **cross-application** workflows: browse the web, read and edit files, write code, fill forms, send emails, etc., within a single task execution.

The defining characteristic is **generality**: a CUA must handle *any* software a human can operate, including proprietary applications with no external API.

## Intuition
A CUA is the "digital employee" vision of AI: give it a task like "Research competitors and prepare a report in Google Docs", and it will open a browser, search, take notes, switch to Docs, and write the report — all autonomously. This goes beyond a single-app automation script because the agent must plan across applications, manage state across context windows, and recover from errors on a live system.

## Historical Context
The term "computer use" was popularized by Anthropic's **Claude Computer Use** (late 2024) and OpenAI's **Computer-Using Agent** (January 2025). These commercial releases validated the paradigm and accelerated open research.

Open-source CUA systems followed quickly: **Agent S** / **Agent S2**, **OpenCUA**, **OS-Copilot**, **ComputerRL**, **UltraCUA**, and **UFO2** (Desktop AgentOS).

## Key Variants / Subtypes

- **Vision-only CUAs**: rely exclusively on screenshots; no accessibility APIs. Maximum cross-platform coverage. Example: Claude Computer Use, UI-TARS.
- **Hybrid-action CUAs**: combine screenshot-level GUI control with API / tool calls. Example: ComputerRL, UltraCUA, UFO2.
- **Agentic framework CUAs**: wrap a VLM with memory, planner, tool interfaces (search, code execution). Example: Agent S, Agent S2, OS-Copilot.
- **RL-trained CUAs**: use RLVR / GRPO for online / semi-online improvement. Example: UI-TARS, ComputerRL, OpenCUA.

## Technical Details

### The CUA pipeline

```
User instruction
    │
    ▼
Observation: screenshot [+ accessibility tree] [+ history summary]
    │
    ▼
VLM backbone: plan → reason → select action
    │
    ▼
Action: click(x,y) / type(text) / scroll / key / call_api(fn, args)
    │
    ▼
Environment: OS / browser / app responds
    │
    ▼
Verifier: rule check / state diff / LLM judge → reward
    │
    ▼
(loop until task complete or step budget exhausted)
```

### API-GUI hybrid action space
Real-world computer use benefits from two action tiers:

| Tier | Example | When to use |
|------|---------|------------|
| **GUI (low-level)** | `click(450, 320)` | No API available; visual confirmation needed |
| **API / Tool (high-level)** | `read_file(path)`, `search_web(query)` | API exists; deterministic; shorter trajectory |

Agents like [[lai-2025-computerrl]] and [[yang-2025-ultracua]] demonstrate that hybrid routing dramatically reduces trajectory length. See [[api-gui-hybrid-action-space]].

### Evaluation
CUAs are typically evaluated on **execution-based** benchmarks where task completion is verified by inspecting real side-effects:
- **OSWorld** (`2404.07972`): 369 tasks in a real Linux VM.
- **Windows Agent Arena** (`2409.08264`): Windows-focused multi-modal tasks.
- **ComputerRL** (`2508.14040`): end-to-end online RL with execution-based reward.

## Key Papers

| Paper | Year | Contribution |
|-------|------|--------------|
| [[xie-2024-osworld]] | 2024 | Core desktop CUA benchmark; 369 tasks; execution-based evaluation; first real-computer RL environment. |
| [[wang-2025-opencua]] | 2025 | Open data, model, and training infrastructure for CUAs. |
| [[lai-2025-computerrl]] | 2025 | End-to-end online RL; API-GUI hybrid action space; entropy-collapse mitigation. |
| [[agashe-2025-agent-s2]] | 2025 | Compositional generalist-specialist CUA; SOTA on OSWorld. |
| [[yang-2025-ultracua]] | 2025 | Foundation CUA with hybrid action; visual primitives + programmatic tool routing. |

## Comparison with Related Concepts

|                   | [[gui-agent]]           | Computer-Use Agent          | API Agent                 |
|-------------------|-------------------------|-----------------------------|---------------------------|
| Platform scope    | Single (web/mobile/OS)  | Cross-platform               | API-accessible services   |
| Action modality   | GUI pixel-level         | GUI + API / tools            | API calls only            |
| Memory / state    | Per-episode context     | Persistent + cross-app memory | Stateless calls           |
| Typical evaluation | OSWorld, WebArena       | OSWorld, Windows Arena       | SWE-bench, code exec      |

## Current Research Frontiers
- **Cross-platform generalization** without platform-specific fine-tuning.
- **Persistent memory** across long tasks and sessions.
- **Permission and safety layers**: what can a CUA do without explicit per-step user confirmation?
- **World-model-based planning**: see [[gui-world-model]].
- **I/O-efficient training**: see [[gui-io-wall]].

## See Also
- [[gui-agent]]
- [[gui-grounding]]
- [[api-gui-hybrid-action-space]]
- [[reinforcement-learning-with-verifiable-rewards]]
- [[gui-io-wall]]
- [[multi-turn-gui-rl]]
