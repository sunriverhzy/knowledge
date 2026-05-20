---
title: "Agentic RL"
aliases: ["Agentic Reinforcement Learning", "RL for Agents", "Agent RL", "Agentic RL for GUI", "智能体强化学习"]
domain: agent
tags: [agent, rl, gui-agent, computer-use, rlvr, grpo, multi-turn, online-rl, agentic-rl]
source_count: 24
last_updated: 2026-05-20
---

# Agentic RL（智能体强化学习）

## 定义
**Agentic RL** 是用强化学习训练**在真实或模拟环境中进行长时程交互以完成复杂任务的自主智能体**。它与标准 RLHF / 文本生成 RL 的区别在于五个结构性特征：

1. **环境交互** —— agent 在真实世界（GUI、OS、浏览器、手机）中行动，而不是对静态文本偏好对做选择。
2. **轨迹级优化** —— 对 20–200+ 步轨迹的优化，而非单个 response 级。
3. **可验证环境奖励（RLVR）** —— 奖励来自客观状态变化（URL、DB、文件、DOM），而非学得的奖励模型。
4. **长时程 credit assignment** —— 第 200 步的成功信号必须传播回第 5 步的动作。
5. **基础设施即算法** —— 并行模拟器、异步 rollout、world model、KV 压缩是第一级设计选择，不是实现细节。

$$
\text{Agentic RL} = \text{RLVR} + \text{多轮优化} + \text{环境交互} + \text{可扩展基础设施}
$$

## 直觉
标准 RLHF 问的是："给定这个 prompt，人类更偏好哪个 response？"
Agentic RL 问的是："给定这个 200 步的 computer 任务，agent 能否通过在真实环境中试错来从零学会完成它？"
Agent 必须感知、规划、行动、验证、从错误中恢复、并改进——全部通过环境本身的 RL 信号。

## 历史背景

| 时期 | 里程碑 |
|------|--------|
| 2017 | AlphaGo/Zero：稀疏终止奖励 + 自博弈的 RL |
| 2022 | RLHF（InstructGPT）：面向文本生成对齐的 RL |
| 2024 | DigiRL：第一个面向 device-control agent 的 offline-to-online RL |
| 2025 Q1 | DeepSeek-R1 / GUI-R1 / UI-R1：GRPO + RLVR → 涌现推理 |
| 2025 Q2–Q4 | WebRL、MobileRL、ComputerRL、Hi-Agent：面向 GUI 的在线 / 层次化 / hybrid RL |
| 2026 | Mobile-Agent-v3.5（MRPO）、Continual GUI Agents、ACuRL、VAGEN：多平台 RL、持续 RL、agentic 验证 |

## Agentic RL 训练流水线

```
┌─────────────────────────────────────────────────────────────────────┐
│  Stage 0: VLM Backbone 预训练（Qwen-VL / InternVL / ...）           │
├─────────────────────────────────────────────────────────────────────┤
│  Stage 1: 在 Demonstration 数据上 SFT                               │
│  ├── 人类/合成轨迹（AitW, Mind2Web, UI-TARS data）                   │
│  ├── Reasoning augmentation：(State, Thought, Action) 三元组         │
│  └── 目标：学会基本 UI 语义，"安全初始化"                            │
├─────────────────────────────────────────────────────────────────────┤
│  Stage 2: Offline RL / Preference Learning                          │
│  ├── DPO on 成功/失败 trajectory pairs                               │
│  ├── 或：Offline GRPO with verifiable rewards                        │
│  └── 目标：从静态数据中学会"哪条轨迹更好"                           │
├─────────────────────────────────────────────────────────────────────┤
│  Stage 3: Online / Semi-online RL                                   │
│  ├── Agent 与真实环境 (AndroidWorld/WebArena/OSWorld) 或              │
│  │   world model (DynaWeb/ViMo/Code2World) 交互                      │
│  ├── GRPO / M-GRPO + 可验证环境奖励                                  │
│  ├── Curriculum learning：failure-set → simplified variants           │
│  └── 目标：学会恢复、适应、长时程规划                                │
├─────────────────────────────────────────────────────────────────────┤
│  Stage 4: Continual RL / Self-improvement（前沿方向）                 │
│  ├── Continual GUI Agents (GUI-AiF)：不遗忘地持续适应                │
│  ├── ACuRL：自主 curriculum，零人工数据                               │
│  └── 目标：agent 跟随 app 演化而自动改进                             │
└─────────────────────────────────────────────────────────────────────┘
```

## 数据构造范式

| 范式 | 来源 | 示例 |
|------|------|------|
| **人类 demonstration** | 真实用户交互录制的轨迹 | AitW (715K), Mind2Web, UI-TARS data |
| **MCTS / 搜索式合成** | 引导树搜索发现高质量轨迹 | Agent Q, M2-Miner |
| **RLVR 在线采样** | Agent 与环境交互获得可验证奖励 | DigiRL, WebRL, MobileRL, ComputerRL |
| **World model dreaming** | 预测下一状态无需真实 env；1000× 吞吐 | DynaWeb, WebWorld, Code2World, ViMo |
| **持续自生成** | Agent 探索新环境 → 评估 → 生成 curriculum → 训练 | ACuRL, GUI-AiF |

## Agentic RL 的 RL 算法变体

| 算法 | 面向 agent 的关键适配 |
|------|----------------------|
| **GRPO** | 无 critic；组归一化 advantage；对 7B–72B VLM 显存友好 |
| **M-GRPO** | 整条轨迹作为优化单元（非单步） |
| **AdaGRPO** | 难度加权：低成功率任务获得更高梯度 |
| **AEPO** | 连续坐标空间的自适应探索 |
| **GuAE** | 基于锚点的 tempering 防止 advantage collapse |
| **MRPO** | 多平台 RL 防止跨平台冲突 |
| **Hierarchical GRPO** | Planner + Executor 双层优化 |
| **DPO** | 来自成功/失败轨迹的 offline 偏好对 |
| **IQL/CQL + Best-of-N** | 基于价值的 offline RL + 推理时动作 reranking |
| **MCTS + DPO** | 搜索生成高质量偏好数据 |

## 奖励架构（三层金字塔）

```
┌─────────────────────────────────────────┐
│  第 3 层：LLM-as-Judge / Learned RM     │  ← 通用但有 reward hacking 风险
├─────────────────────────────────────────┤
│  第 2 层：过程奖励（ProgRM/PRM）        │  ← 稠密但需自动提取里程碑
├─────────────────────────────────────────┤
│  第 1 层：基于规则的可验证奖励          │  ← 无噪声但稀疏
│  (URL match, bbox IoU, DB check)        │
└─────────────────────────────────────────┘
```

SOTA 系统使用**组合奖励**结合三层。

## 基础设施需求

| 挑战 | 解法 | 示例 |
|------|------|------|
| GUI I/O 延迟 (0.5–2s/step) | 并行环境 × 512 | MAI-UI |
| GPU 等 env 响应时空闲 | 异步 Actor-Trainer 分离 | AReaL |
| 轨迹 > 上下文窗口 | 动态上下文压缩 | WebAgent-R1 |
| 环境不可复现 | World model dream rollout | DynaWeb |
| KV cache 爆炸 | 时空 KV 压缩 | GUI-KV |
| 真实环境探索不安全 | Offline → Semi-online → Online | DigiRL 流水线 |

## 关键论文

| 论文 | 年份 | 核心 Agentic RL 贡献 |
|------|------|---------------------|
| [[bai-2024-digiirl]] | 2024 | 经典 offline-to-online RL；VLM-as-evaluator |
| [[luo-2025-gui-r1]] | 2025 | RLVR + GRPO；3K 样本 → 涌现推理 |
| [[qi-2024-webrl]] | 2024 | Self-evolving curriculum + ORM |
| [[wei-2025-webagent-r1]] | 2025 | M-GRPO：轨迹级多轮优化 |
| [[lai-2025-computerrl]] | 2025 | 端到端 online RL；API-GUI hybrid；Entropulse |
| [[wu-2025-hi-agent]] | 2025 | Hierarchical Planner+Executor GRPO；87.9% AitW |
| [[xu-2025-mobilerl]] | 2025 | AdaGRPO；难度自适应 mobile RL |
| [[ding-2026-dynaweb]] | 2026 | Web World Model；model-based RL |
| [[xu-2026-mobile-agent-v3-5]] | 2026 | MRPO；多平台 RL；20+ benchmark SOTA |
| [[liu-2026-continual-gui-agents]] | 2026 | 第一个 GUI agent 持续 RL 框架 |
| [[guo-2026-acurl]] | 2026 | 自主 curriculum RL；零人工数据 |
| [[cui-2026-agentic-reward-modeling]] | 2026 | VAGEN：主动 verifier agent for RLVR |
| [[hu-2026-faithful-mobile-gui]] | 2026 | GuAE：解决稀疏 GUI RL 中的 advantage collapse |

## 与相关概念的对比

|                       | Agentic RL                    | [[reinforcement-learning-from-human-feedback]] | 监督式 Agent 训练（SFT/BC） |
|-----------------------|-------------------------------|------------------------------------------------|--------------------------|
| 交互对象              | 真实环境                      | 静态偏好对                                      | 无（离线 demonstration） |
| 时程                  | 20–200+ 步                    | 1 个 response                                  | 1 个 response / 固定轨迹 |
| 奖励来源              | 环境验证                      | 学得的人类偏好模型                              | N/A（teacher forcing）    |
| Credit assignment     | 困难（长时程）                | 简单（单 response）                            | N/A                       |
| 所需基础设施          | 并行 env、world model         | 标准 GPU 训练                                  | 标准 GPU 训练             |
| 失败模式              | 不安全动作、env 延迟           | [[reward-hacking]]                             | 复合误差（covariate shift）|
| 能发现新策略吗？       | ✅ 能                         | 部分                                            | ❌ 不能（受 demo 限制）   |

## 当前研究前沿
- **持续 Agentic RL**：agent 在目标环境演化时持续改进。
- **长时程 agent 任务的过程奖励**：无需人工标注的稠密 credit。
- **World model 保真度**：dream rollout 能否替代真实环境交互？
- **不可逆环境中的安全探索**：在线 RL 期间防止灾难性动作。
- **多平台泛化**：一次 RL 训练覆盖 web、mobile、desktop。
- **Agentic 验证（VAGEN）**：用 RL 训练的 verifier agent 为其他 agent 产出奖励。

## 参见
- [[gui-agent]]
- [[computer-use-agent]]
- [[reinforcement-learning-with-verifiable-rewards]]
- [[group-relative-policy-optimization]]
- [[multi-turn-gui-rl]]
- [[gui-io-wall]]
- [[gui-world-model]]
- [[api-gui-hybrid-action-space]]
- [[gui-grounding]]
