---
title: "GUI Agent / Computer Use SOTA 调研综述笔记"
domain: [agent, multimodal, rl]
difficulty: advanced
last_updated: 2026-05-20
source_review: "arXiv:2604.27955"
raw_manifest: "../../raw/gui_agent_computer_use_sota_download_manifest.txt"
---

# GUI Agent / Computer Use SOTA 调研综述笔记

> 范围：本笔记围绕 **Frontier Models、RL Methods、Benchmarks、Grounding、Mobile、Desktop、Web Agents** 总结 GUI Agent / Computer Use 方向的代表性工作，主要依据 *GUI Agents with Reinforcement Learning: Toward Digital Inhabitants*（`arXiv:2604.27955`）以及已下载到 `raw/gui_agent_computer_use_sota_download_manifest.txt` 的论文集合。
>
> 日期：2026-05-20。该领域变化很快，因此本文应被视为一个阶段性快照，而非永久排行榜。

---

## 执行摘要

当前 SOTA 的重点已经不再是"提示一个 VLM 去点按钮"，而是转向**经过 RL 训练、由环境反馈约束、能进行多轮任务执行的 computer-use agents**。最强系统通常组合了：

1. **原生截图感知**，而不是脆弱的 DOM / accessibility-only 管线。
2. **RL fine-tuning**，尤其是 [[group-relative-policy-optimization]] 与基于可验证奖励的 RL（RLVR）。
3. **Hybrid offline-to-online training**：SFT / BC 用于安全初始化，离线偏好或价值学习用于规模化，在线 / 半在线 RL 用于恢复能力与分布校正。
4. **跨平台动作空间**，覆盖 desktop、mobile、web。
5. **稠密奖励接口**：规则检查、IoU grounding reward、outcome-supervised reward model、model-as-judge 奖励、process / progress reward。
6. **基础设施级扩展**：浏览器 / VM / 模拟器并行、异步 rollout，以及越来越重要的 world-model / simulator-assisted training，用来突破 GUI I/O wall。

一个有用的心智模型：

```text
SOTA GUI agent = VLM backbone + action model + grounding module + memory/planning + RLVR/GRPO + environment verifier + rollout infrastructure
```

最强的研究方向是**面向可验证 computer use 的 hybrid RL**：先用离线数据学安全 UI 语义，再用在线 / 半在线交互学习恢复、效率与长时程规划。

---

## 1. Frontier Models

### 1.1 闭源前沿系统

| 系统 | 来源 | 核心思想 | 备注 |
|---|---|---|---|
| OpenAI Computer-Using Agent / Operator | OpenAI 官网，无 PDF | 带 RL 增强的自主 computer control，面向可验证结果任务 | 重要商业基线；`raw/` 中没有 arXiv PDF。 |
| Claude Computer Use | Anthropic 官网，无 PDF | 基于纯截图的 computer-use tool calling，安全训练上继承 Constitutional AI / RLHF 路线 | 视觉-only computer control 的强代表。 |
| Gemini 2.5 Computer Use | Google blog / model release | 面向浏览器 / 应用交互的商业 computer-use model | 未找到论文 PDF。 |

这些系统重要，因为它们验证了产品方向；但训练数据、奖励设计与完整 benchmark 结果不透明，因此难以做严格学术分析。

### 1.2 开源 / 有论文支撑的前沿系统

| 模型 / 论文 | 类别 | 核心贡献 | 已下载 PDF |
|---|---|---|---|
| `UI-TARS` (`2501.12326`) | 通用 GUI agent | 原生截图-only GUI agent，跨平台，"thoughts-before-actions"，benchmark 覆盖强 | `raw/2501.12326 - UI-TARS - Pioneering Automated GUI Interaction with Native Agents.pdf` |
| `UI-TARS-2` (`2509.02544`) | 多轮 RL GUI agent | 用多轮 RL 推进 UI-TARS，增强 computer-use 能力 | `raw/2509.02544 - UI-TARS-2 Technical Report - Advancing GUI Agent with Multi-turn Reinforcement Learning.pdf` |
| `OpenCUA` (`2508.09123`) | computer-use 开放基座 | 面向 computer-use agents 的开放数据 / 模型 / 训练基础 | `raw/2508.09123 - OpenCUA - Open Foundations for Computer-use Agents.pdf` |
| `ComputerRL` (`2508.14040`) | 端到端 online RL computer use | API-GUI hybrid action space；处理 entropy collapse 与 I/O-bound online RL | `raw/2508.14040 - ComputerRL - Scaling End-to-end Online Reinforcement Learning for Computer Use Agents.pdf` |
| `Agent S` (`2410.08164`) | 开放 computer-use 框架 | 像人一样使用电脑的 agentic 框架 | `raw/2410.08164 - Agent S - An Open Agentic Framework That Uses Computers Like a Human.pdf` |
| `Agent S2` (`2504.00906`) | generalist-specialist computer-use agent | 组合式 generalist-specialist 架构 | `raw/2504.00906 - Agent S2 - A Compositional Generalist-specialist Framework for Computer Use Agents.pdf` |
| `OS-ATLAS` (`2410.23218`) | Foundation action model | 面向 generalist GUI agents 的基础动作模型 | `raw/2410.23218 - OS-ATLAS - A Foundation Action Model for Generalist GUI Agents.pdf` |
| `Aguvis` (`2412.04454`) | 纯视觉 GUI agent | 统一的纯视觉 autonomous GUI interaction agent | `raw/2412.04454 - Aguvis - Unified Pure Vision Agents for Autonomous GUI Interaction.pdf` |
| `ShowUI` (`2411.17465`) | Vision-language-action model | 面向 GUI visual agent 的单一 VLA 模型 | `raw/2411.17465 - ShowUI - One Vision-language-action Model for GUI Visual Agent.pdf` |
| `Falcon-UI` (`2412.09362`) | GUI 预训练 | 先理解 GUI 再跟随用户指令；强调 GUI 表征学习 | `raw/2412.09362 - Falcon-UI - Understanding GUI Before Following User Instructions.pdf` |
| `InfiGUIAgent` (`2501.04575`) | 推理 / 反思 | 带原生推理与反思的多模态 generalist GUI agent | `raw/2501.04575 - InfiGUIAgent - A Multimodal Generalist GUI Agent with Native Reasoning and Reflection.pdf` |
| `UFO2` (`2504.14603`) | Desktop AgentOS | 桌面 AgentOS | `raw/2504.14603 - UFO2 - The Desktop AgentOS.pdf` |
| `UltraCUA` (`2510.17790`) | hybrid action CUA 基座 | 结合视觉 primitive 与 API/tool action 的 computer use agent | `raw/2510.17790 - UltraCUA - A Foundation Model for Computer Use Agents with Hybrid Action.pdf` |

### 1.3 Frontier-model 小结

前沿模型正在分叉：

- **原生视觉 agent**：UI-TARS、Aguvis、ShowUI、Falcon-UI、InfiGUIAgent。它们优先追求跨平台泛化。
- **Hybrid-action computer-use agent**：ComputerRL、UltraCUA、UFO2、OS-Copilot。它们把低层 GUI 控制与高层 API/tools 结合，降低轨迹长度并提升可靠性。

最可能胜出的架构不是 pure GUI，也不是 pure API，而是 **API-GUI hybrid**：没有 API 时用 GUI；API 更安全、更短时用 API。

---

## 2. RL Methods

### 2.1 Offline RL / Offline RFT

Offline 方法是安全层：让 agent 在接触真实系统前学会基本 UI 语义。

| 方法族 | 代表论文 | 解决什么 | 弱点 |
|---|---|---|---|
| Behavioral cloning / SFT | Android in the Wild、UI-TARS data | 安全初始化 | 分布偏移下错误会复合 |
| Value-based offline RL | `Digi-Q` | 从静态 device-control 数据学 action value；支持 Best-of-N reranking | 需要价值估计；存在 OOD action evaluation 风险 |
| Preference optimization | `UI-TARS`、`Agent Q`、ARPO | 使用成功 vs 失败轨迹；对大 VLM 更稳定 | 依赖高质量偏好对 / 搜索数据 |
| Offline GRPO / RLVR | `GUI-R1`、`UI-R1` | 用可验证奖励，无需 learned critic | 最适合奖励能干净验证的场景 |

关键论文：

- `Digi-Q` (`2502.15760`)：冻结 VLM backbone + Q-value head + Best-of-N action reranking。
- `UI-TARS` (`2501.12326`)：大规模跨平台 agent，SFT + RL-style improvement。
- `Agent Q` (`2408.07199`)："search is data"；MCTS 为 DPO 发现高质量 web 轨迹。
- `GUI-R1` (`2504.10458`) 与 `UI-R1` (`2503.21620`)：基于规则 RLVR / GRPO + 统一 GUI action space。

### 2.2 Online RL

Online RL 对恢复能力、适应能力、长时程 credit assignment 很关键，但受限于 GUI I/O 延迟。

| 方法 | 核心思想 | 为什么重要 |
|---|---|---|
| `WebRL` (`2411.02337`) | 面向 web agents 的 self-evolving online curriculum | Curriculum learning 取代随机探索。 |
| `MobileRL` (`2509.18119`) | 移动 GUI 上的 AdaGRPO + 难度加权 | 处理长尾任务难度，抑制冗余操作。 |
| `WebAgent-R1` (`2505.16421`) | 对完整 web 轨迹做 Multi-turn GRPO | 优化整条轨迹，而不是单步动作。 |
| `ComputerRL` (`2508.14040`) | 面向 computer use 的端到端 online RL | 把 computer-use 训练视为系统 + RL 问题。 |

Online RL 的瓶颈不只是算法成熟度，而是 **I/O wall**：截图、渲染、DOM 解析、网络请求、模拟器或 VM 延迟。这也是 pure online RL 很少足够的原因。

### 2.3 Hybrid RL：当前主导范式

Hybrid 方法把安全初始化与自适应交互结合：

```text
SFT / BC → offline RL or preference learning → semi-online / online RL → verifier-guided self-improvement
```

代表系统：

| 系统 | Hybrid 形态 | 关键思想 |
|---|---|---|
| `DigiRL` (`2406.11896`) | Offline-to-online | 从 Android in the Wild 初始化，再用 VLM evaluator 在线微调。 |
| `UI-S1` (`2509.11543`) | Semi-online RL | 用 trajectory patching 在静态数据上模拟在线纠错。 |
| `MANO` (`2509.17336`) | SFT → Offline RL → Online RL | 三阶段 GRPO + composite rewards + closed-loop data cycle。 |
| `DynaWeb` (`2601.22149`) | Model-based RL | 用 web world model dream rollout，减少真实交互成本。 |
| `Hi-Agent` (`2510.14388`) | Hierarchical RL | Planner + executor；训练 subgoal-level 与 UI-action-level 策略。 |
| `UltraCUA` (`2510.17790`) | Hybrid action space | 在 GUI actions 与 APIs/tools 之间路由。 |

### 2.4 RL-method 小结

主流算法栈是：

- **DPO / preference optimization**：用于离线轨迹对比。
- **GRPO / RLVR**：用于可验证 GUI 奖励和显存友好的 VLM RL。
- **Curriculum learning**：用于冷启动与长尾任务。
- **World models / synthetic rollouts**：用于突破 GUI I/O wall。
- **Hierarchical RL**：用于长时程 credit assignment。

这与现有 Wiki 概念直接相连：[[group-relative-policy-optimization]]、[[reinforcement-learning-from-human-feedback]]、[[reward-hacking]]、[[chain-of-thought]]。

---

## 3. Benchmarks / Environments

在 GUI RL 中，benchmark 不只是评测工具，也是**训练环境**和**奖励提供者**。

### 3.1 Cross-platform / desktop / OS

| Benchmark / Environment | Platform | 作用 | PDF |
|---|---|---|---|
| `OSWorld` (`2404.07972`) | Desktop / OS | 真实电脑环境，369 个开放任务；execution-based evaluation | `raw/2404.07972 - OSWorld - Benchmarking Multimodal Agents for Open-ended Tasks in Real Computer Environments.pdf` |
| `Windows Agent Arena` (`2409.08264`) | Windows | 可扩展 Windows OS-agent 评测 | `raw/2409.08264 - Windows Agent Arena - Evaluating Multi-modal OS Agents at Scale.pdf` |
| `OmniAct` (`2402.17553`) | Desktop + web | 面向 multimodal generalist autonomous agents 的数据集 / 基准 | `raw/2402.17553 - OmniAct - A Dataset and Benchmark for Enabling Multimodal Generalist Autonomous Agents.pdf` |
| `ScreenAgent` (`2402.07945`) | Desktop | VNC / pixel-stream computer-control agent | `raw/2402.07945 - ScreenAgent - A Vision Language Model-driven Computer Control Agent.pdf` |

### 3.2 Web / browser

| Benchmark / Environment | 作用 | PDF |
|---|---|---|
| `WebArena` (`2307.13854`) | 带 executable verification 的 realistic self-hosted web environment | `raw/2307.13854 - WebArena - A Realistic Web Environment for Building Autonomous Agents.pdf` |
| `VisualWebArena` (`2401.13649`) | 加入视觉 web 任务；多模态 web-agent benchmark | `raw/2401.13649 - VisualWebArena - Evaluating Multimodal Agents on Realistic Visual Web Tasks.pdf` |
| `BrowserGym` (`2412.05467`) | WebArena、MiniWoB++、VisualWebArena、WebChoreArena 的统一 API / ecosystem | `raw/2412.05467 - BrowserGym - The BrowserGym Ecosystem for Web Agent Research.pdf` |
| `Mind2Web` (`2306.06070`) | Web demonstration / trajectory dataset；语义 DOM action filtering | `raw/2306.06070 - Mind2Web - Towards a Generalist Agent for the Web.pdf` |
| `WebVoyager` (`2401.13919`) | 端到端 multimodal web agent baseline | `raw/2401.13919 - WebVoyager - Building an End-to-end Web Agent with Large Multimodal Models.pdf` |

### 3.3 Mobile

| Benchmark / Environment | 作用 | PDF |
|---|---|---|
| `Android in the Wild` (`2307.10088`) | 大规模 Android device-control 数据集 | `raw/2307.10088 - Android in the Wild - A Large-scale Dataset for Android Device Control.pdf` |
| `AndroidWorld` (`2405.14573`) | 动态 mobile RL environment，带参数化任务与可验证奖励 | `raw/2405.14573 - AndroidWorld - A Dynamic Benchmarking Environment for Autonomous Agents.pdf` |

### 3.4 Benchmark 小结

最好的 benchmark 共享三个性质：

1. **Executable verification**：检查真实副作用，而不是文本自述。
2. **Resettable environments**：在线 RL 和反复 rollout 必须能 reset。
3. **Diverse task families**：防止死记硬背，支持 curriculum learning。

因此，`OSWorld`、`WebArena`、`VisualWebArena`、`AndroidWorld` 是当前 GUI / computer-use 研究的核心环境四件套。

---

## 4. Grounding / Perception

Grounding 是局部控制瓶颈：规划再好，点偏几个像素也会失败。

### 4.1 核心 grounding 论文与数据集

| 论文 / 数据集 | 主要贡献 | PDF |
|---|---|---|
| `SeeClick` (`2401.10935`) | 用 GUI grounding 推进 visual GUI agents | `raw/2401.10935 - SeeClick - Harnessing GUI Grounding for Advanced Visual GUI Agents.pdf` |
| `Ferret-UI` (`2404.05719`) | 面向 mobile UI 的 grounded MLLM understanding | `raw/2404.05719 - Ferret-UI - Grounded Mobile UI Understanding with Multimodal LLMs.pdf` |
| `Ferret-UI 2` (`2410.18967`) | 跨平台 universal UI understanding | `raw/2410.18967 - Ferret-UI 2 - Mastering Universal User Interface Understanding Across Platforms.pdf` |
| `ScreenSpot-Pro` (`2504.07981`) | 面向专业高分辨率 computer use 的 grounding benchmark | `raw/2504.07981 - ScreenSpot-Pro - GUI Grounding for Professional High-resolution Computer Use.pdf` |
| `OS-ATLAS` (`2410.23218`) | Foundation action model；大规模 action grounding | `raw/2410.23218 - OS-ATLAS - A Foundation Action Model for Generalist GUI Agents.pdf` |
| `GUI-G2` (`2507.15846`) | 面向 GUI grounding 的 Gaussian reward modeling | `raw/2507.15846 - GUI-G2 - Gaussian Reward Modeling for GUI Grounding.pdf` |
| `UI-AGILE` (`2507.22025`) | 稠密距离 / IoU-style grounding reward + inference-time grounding | `raw/2507.22025 - UI-AGILE - Advancing GUI Agents with Effective Reinforcement Learning and Precise Inference-time Grounding.pdf` |
| `InfiGUI-G1` (`2508.05731`) | 用 Adaptive Exploration Policy Optimization 做 grounding | `raw/2508.05731 - InfiGUI-G1 - Advancing GUI Grounding with Adaptive Exploration Policy Optimization.pdf` |
| `GUI-Eyes` (`2601.09770`) | grounding 前主动调用视觉工具的 tool-augmented active perception | `raw/2601.09770 - GUI-Eyes - Tool-augmented Perception for Visual Grounding in GUI Agents.pdf` |
| `SE-GUI` (`2505.12370`) | 基于 attention pseudo-label 的 self-evolutionary RL | `raw/2505.12370 - SE-GUI - Enhancing Visual Grounding for GUI Agents via Self-evolutionary Reinforcement Learning.pdf` |

### 4.2 Grounding 关键洞察

对 grounding 来说，**更多推理不一定更好**。综述指出，`InfiGUI-G1` 发现显式 CoT 会因为 hallucination 降低坐标精度。这暗示未来需要双系统架构：

- **System 1**：快速、直接的坐标 grounding，用于局部动作。
- **System 2**：较慢的推理，用于规划、恢复和多步任务分解。

开放问题是：如何学习什么时候在两者之间路由。

---

## 5. Mobile Agents

Mobile GUI 是 SOTA 竞争的重要战场，因为 Android 同时提供了强 instrumentation（`ADB`、SQLite state inspection、模拟器扩展）和真实的视觉 / 手势交互复杂性。

### 5.1 核心 mobile 论文

| 论文 | 作用 | PDF |
|---|---|---|
| `Android in the Wild` (`2307.10088`) | 大规模真实 Android 轨迹 | `raw/2307.10088 - Android in the Wild - A Large-scale Dataset for Android Device Control.pdf` |
| `AndroidWorld` (`2405.14573`) | 动态 mobile benchmark / RL environment | `raw/2405.14573 - AndroidWorld - A Dynamic Benchmarking Environment for Autonomous Agents.pdf` |
| `DigiRL` (`2406.11896`) | device-control agents 的 offline-to-online RL | `raw/2406.11896 - DigiRL - Training In-the-wild Device-control Agents with Autonomous Reinforcement Learning.pdf` |
| `Digi-Q` (`2502.15760`) | Offline Q-value learning + Best-of-N reranking | `raw/2502.15760 - Digi-Q - Learning Q-value Functions for Training Device-control Agents.pdf` |
| `Mobile-Agent-v3` (`2508.15144`) | Fundamental mobile / GUI automation agent | `raw/2508.15144 - Mobile-Agent-v3 - Fundamental Agents for GUI Automation.pdf` |
| `AgentCPM-GUI` (`2506.01391`) | 通过 reinforcement fine-tuning 构建 mobile-use agents | `raw/2506.01391 - AgentCPM-GUI - Building Mobile-use Agents with Reinforcement Fine-tuning.pdf` |
| `MagicGUI` (`2508.03700`) | Foundational mobile GUI agent + scalable data pipeline + RFT | `raw/2508.03700 - MagicGUI - A Foundational Mobile GUI Agent with Scalable Data Pipeline and Reinforcement Fine-tuning.pdf` |
| `MobileRL` (`2509.18119`) | Mobile GUI agents 的 online agentic RL | `raw/2509.18119 - MobileRL - Online Agentic Reinforcement Learning for Mobile GUI Agents.pdf` |
| `Hi-Agent` (`2510.14388`) | 面向 mobile device control 的 hierarchical VLA agent | `raw/2510.14388 - Hi-Agent - Hierarchical Vision-language Agents for Mobile Device Control.pdf` |

### 5.2 Mobile 小结

Mobile agents 是 **offline-to-online RL** 最成熟的方向之一：

- `Android in the Wild` 提供 demonstration。
- `AndroidWorld` 提供可 reset 的交互任务和 ground-truth check。
- `DigiRL` / `Digi-Q` 展示 offline-to-online 与 offline value-learning 模式。
- `MobileRL` 与 `Hi-Agent` 推进 difficulty-aware 和 hierarchical online RL。

下一阶段前沿是降低模拟器成本，以及在真实用户数据约束下进行安全探索。

---

## 6. Desktop / OS Agents

Desktop / OS agents 需要跨应用 workflow、文件系统状态跟踪、窗口管理、多应用记忆。这比单应用 mobile 或单站点 web 任务更接近真实的 "computer use"。

### 6.1 核心 desktop / OS 论文

| 论文 | 作用 | PDF |
|---|---|---|
| `OSWorld` (`2404.07972`) | 核心 desktop / real-computer benchmark | `raw/2404.07972 - OSWorld - Benchmarking Multimodal Agents for Open-ended Tasks in Real Computer Environments.pdf` |
| `Windows Agent Arena` (`2409.08264`) | 可扩展 Windows OS-agent benchmark | `raw/2409.08264 - Windows Agent Arena - Evaluating Multi-modal OS Agents at Scale.pdf` |
| `ScreenAgent` (`2402.07945`) | 基于 VNC 的 pixel-stream computer-control agent | `raw/2402.07945 - ScreenAgent - A Vision Language Model-driven Computer Control Agent.pdf` |
| `OS-Copilot` (`2402.07456`) | 带 self-improvement 的 generalist computer agents | `raw/2402.07456 - OS-Copilot - Towards Generalist Computer Agents with Self-improvement.pdf` |
| `UFO2` (`2504.14603`) | Desktop AgentOS | `raw/2504.14603 - UFO2 - The Desktop AgentOS.pdf` |
| `ComputerRL` (`2508.14040`) | 面向 computer-use agents 的端到端 online RL | `raw/2508.14040 - ComputerRL - Scaling End-to-end Online Reinforcement Learning for Computer Use Agents.pdf` |
| `Ponder & Press` (`2412.01268`) | 推动 visual GUI agent 走向 general computer control | `raw/2412.01268 - Ponder and Press - Advancing Visual GUI Agent Towards General Computer Control.pdf` |
| `OpenCUA` (`2508.09123`) | computer-use agents 的开放基础 | `raw/2508.09123 - OpenCUA - Open Foundations for Computer-use Agents.pdf` |

### 6.2 Desktop 小结

Desktop agents 暴露了最难版本的 computer use：

- 状态分布在文件、应用、窗口、网页之间；
- 评测需要基于执行副作用的 inspection；
- 轨迹长，错误恢复必不可少；
- API-GUI hybrid action space 越来越必要。

`OSWorld` 是 canonical benchmark，而 `ComputerRL`、`OpenCUA`、`UFO2` 指向下一代 OS-level agents。

---

## 7. Web Agents

Web agents 是目前最成熟的环境类别，因为浏览器状态相对容易 instrumentation 和 reset。

### 7.1 核心 web 论文

| 论文 | 作用 | PDF |
|---|---|---|
| `Mind2Web` (`2306.06070`) | Generalist web-agent dataset；deterministic replay 与 DOM action compression | `raw/2306.06070 - Mind2Web - Towards a Generalist Agent for the Web.pdf` |
| `WebArena` (`2307.13854`) | 真实 self-hosted web benchmark | `raw/2307.13854 - WebArena - A Realistic Web Environment for Building Autonomous Agents.pdf` |
| `VisualWebArena` (`2401.13649`) | 需要视觉感知的多模态 web benchmark | `raw/2401.13649 - VisualWebArena - Evaluating Multimodal Agents on Realistic Visual Web Tasks.pdf` |
| `WebVoyager` (`2401.13919`) | 使用大多模态模型的端到端 web agent | `raw/2401.13919 - WebVoyager - Building an End-to-end Web Agent with Large Multimodal Models.pdf` |
| `BrowserGym` (`2412.05467`) | 统一 benchmark / environment API | `raw/2412.05467 - BrowserGym - The BrowserGym Ecosystem for Web Agent Research.pdf` |
| `Agent Q` (`2408.07199`) | MCTS + DPO 的 autonomous web-agent learning | `raw/2408.07199 - Agent Q - Advanced Reasoning and Learning for Autonomous AI Agents.pdf` |
| `WebRL` (`2411.02337`) | Web agents 的 self-evolving online curriculum RL | `raw/2411.02337 - WebRL - Training LLM Web Agents via Self-evolving Online Curriculum Reinforcement Learning.pdf` |
| `WebAgent-R1` (`2505.16421`) | Web agents 的端到端 multi-turn RL | `raw/2505.16421 - WebAgent-R1 - Training Web Agents via End-to-end Multi-turn Reinforcement Learning.pdf` |
| `DynaWeb` (`2601.22149`) | 使用 web world model 的 model-based RL | `raw/2601.22149 - DynaWeb - Model-based Reinforcement Learning of Web Agents.pdf` |

### 7.2 Web 小结

Web agents 是研究 **curriculum RL + executable verification + world models** 的最佳场景。路径大致是：

```text
Mind2Web / WebArena demonstrations → WebRL curriculum → WebAgent-R1 multi-turn GRPO → DynaWeb world-model rollouts
```

BrowserGym 很重要，因为它把碎片化的 web-agent environment 统一成了公共 API。

---

## 8. 跨方向 SOTA 模式

### 模式 A —— 可验证奖励是重心

GUI / computer-use 任务经常暴露客观状态变化：URL transition、DOM state、Android SQLite row、文件系统变化、form submission。相比开放文本生成，它们天然更适合 RLVR。

### 模式 B —— GRPO 正在成为默认 RL 算法

对 VLM-scale GUI agent，GRPO 去掉 learned critic，降低显存并简化训练。它出现在 `GUI-R1`、`UI-R1`、`MANO`、`InfiGUI-G1`、`GUI-Eyes` 及大量更新系统里。

### 模式 C —— Hybrid 胜过 pure offline 或 pure online

Pure offline 有 distribution shift；pure online 昂贵且不安全。实践 SOTA 是 staged / hybrid：

```text
SFT → offline preference/value learning → semi-online correction → selective online RL → verifier-guided self-improvement
```

### 模式 D —— Reasoning 与 grounding 必须分离

Reasoning 有助于规划和恢复，但可能伤害像素级坐标准确率。未来系统很可能需要学习在以下模块间路由：

- 快速视觉 grounding；
- 慢速 deliberative planning；
- memory retrieval；
- verifier / judge 调用；
- API vs GUI actions。

### 模式 E —— 基础设施本身就是算法的一部分

GUI RL 是 I/O-bound。环境吞吐、异步 rollout、浏览器 / VM / 模拟器密度、状态压缩、奖励验证管线都不是实现细节，而会决定哪些算法可行。

---

## 9. 推荐阅读顺序

### Stage 1 —— Foundations and benchmarks
1. `OSWorld` (`2404.07972`)
2. `WebArena` (`2307.13854`)
3. `VisualWebArena` (`2401.13649`)
4. `AndroidWorld` (`2405.14573`)
5. `Mind2Web` (`2306.06070`)

### Stage 2 —— Native GUI / computer-use agents
1. `UI-TARS` (`2501.12326`)
2. `OpenCUA` (`2508.09123`)
3. `Agent S2` (`2504.00906`)
4. `OS-ATLAS` (`2410.23218`)
5. `Aguvis` (`2412.04454`)

### Stage 3 —— RL methods
1. `DigiRL` (`2406.11896`)
2. `Digi-Q` (`2502.15760`)
3. `GUI-R1` (`2504.10458`)
4. `UI-R1` (`2503.21620`)
5. `WebRL` (`2411.02337`)
6. `WebAgent-R1` (`2505.16421`)
7. `ComputerRL` (`2508.14040`)
8. `DynaWeb` (`2601.22149`)

### Stage 4 —— Grounding and perception
1. `SeeClick` (`2401.10935`)
2. `Ferret-UI` (`2404.05719`)
3. `ScreenSpot-Pro` (`2504.07981`)
4. `InfiGUI-G1` (`2508.05731`)
5. `GUI-Eyes` (`2601.09770`)

---

## 10. 开放问题

1. **可靠奖励接口**：如何组合 rule checks、environment feedback、LLM-as-judge 与 learned rewards，同时避免 reward hacking？
2. **I/O-efficient RL**：当每个 GUI step 需要 0.5–2 秒时，如何扩展在线训练？
3. **World-model fidelity**：如何使用 web / GUI world model 而不积累 sim-to-real gap？
4. **Hierarchical control**：agent 什么时候该 think、click、call API、ask for help、backtrack？
5. **Cross-platform generalization**：一个策略能否同时覆盖 web、desktop、mobile，而不过拟合某个平台的 UI 惯例？
6. **Safety and permissions**：如何训练可以在真实电脑上行动、但不会造成不可逆破坏的 agent？
7. **Evaluation drift**：排行榜变化很快；benchmark 需要 live、auditable、side-effect-aware 的评估。

---

## Bottom Line

GUI Agent / Computer Use 的 SOTA 方向可以概括为：

```text
native visual GUI understanding
+ verifiable environment feedback
+ GRPO / RLVR
+ hybrid offline-online training
+ API-GUI hybrid actions
+ asynchronous rollout infrastructure
+ memory / hierarchical planning
```

如果要围绕该方向继续建设研究 Wiki，第一批应创建的概念页是：

- `gui-agent`
- `computer-use-agent`
- `gui-grounding`
- `reinforcement-learning-with-verifiable-rewards`
- `api-gui-hybrid-action-space`
- `gui-io-wall`
- `multi-turn-gui-rl`
- `world-model-for-web-agents`
