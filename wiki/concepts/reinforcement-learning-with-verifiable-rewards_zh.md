---
title: "Reinforcement Learning with Verifiable Rewards"
aliases: ["RLVR", "基于可验证奖励的强化学习"]
domain: agent
tags: [rl, rlvr, agent, gui-agent, grpo, reward-modeling, alignment]
source_count: 12
last_updated: 2026-05-20
---

# 基于可验证奖励的强化学习（RLVR）

## 定义
**基于可验证奖励的强化学习（RLVR）** 是一种 RL 训练范式，其奖励信号来源于**客观的、环境可验证的事实**——例如 URL 是否发生了变化、数据库行是否被更新、坐标是否命中了目标 bounding box——而不是来自学得的奖励模型或人类偏好标注。验证器是环境状态的确定性函数，而非神经网络。

$$
r(s_t, a_t) = \text{verify}(\text{env\_state}_{t+1}, \text{goal})
\in \{0, 1\} \text{ 或 } [0,1]
$$

RLVR 与 RLHF 的区别在于：奖励**不能**通过利用学得奖励模型的弱点来"作弊"——环境状态就是真相来源。

## 直觉
在 GUI / computer-use 任务中，环境提供**无法作弊的反馈**：购买要么成功要么失败；文件要么被创建要么没有。这种确定性可验证性让 GUI 自动化成为 RLVR 的理想测试床——类比于数学 / 代码环境如何让 LLM 推理的 RLVR 成为可能（DeepSeek-R1 风格）。

核心洞察：**GUI 任务是"伪装成感知任务"的 RLVR 任务**——只是需要视觉感知和动作执行，而不是符号证明生成。

## 历史背景
- **DeepSeek-R1（2025）**——证明 GRPO + 基于规则的可验证奖励在数学/代码任务上能涌现 Chain-of-Thought 推理。
- **GUI-R1、UI-R1（2025）**——直接把 RLVR / GRPO 应用到 GUI grounding 和多步任务；涌现了内部独白。
- **DigiRL、WebRL、ComputerRL、AndroidWorld（2024–2025）**——使用基于执行的环境状态作为可验证奖励。

## 关键变体 / 子类型

- **二元 RLVR**：成功 $r = 1$，失败 $r = 0$。干净但稀疏。
- **部分信用 RLVR**（GUI grounding）：精确 / 方向正确 / 错误对应 $r \in \{0, 0.5, 1\}$。
- **连续 RLVR**（grounding）：$r = f(\text{dist, IoU, Gaussian})$。
- **过程级 RLVR**：验证并奖励中间里程碑。见 ProgRM、AgentPRM。
- **复合 RLVR**：组合多个可验证信号（坐标 IoU + 动作类型 + 格式有效性）。

## 技术细节

### 用 GRPO 实现 RLVR

对 GUI agent，[[group-relative-policy-optimization]]（GRPO）是 RLVR 最常用的算法：

1. 对每个任务 prompt，从当前策略采样 $G$ 条轨迹。
2. 在环境中执行，收集可验证奖励 $r_i$。
3. 计算归一化 advantage：$\hat A_i = (r_i - \bar r) / \hat\sigma_r$。
4. 更新策略：$\max_\theta \mathbb{E}\left[\sum_i \hat A_i \log \pi_\theta(a_i | o_i)\right]$，加 KL 惩罚。

不需要 learned critic。可验证环境奖励替代了 critic 的价值估计。

### 为什么 RLVR 比 RLHF 更适合 GUI 任务

| 维度 | RLHF（learned RM） | RLVR（环境 verifier） |
|------|--------------------|----------------------|
| 奖励来源 | 学得的模型 | 确定性环境 |
| 奖励作弊 | 风险高 | 风险低（verifier 无法被愚弄） |
| 所需数据 | 偏好对 | 可执行任务定义 |
| 可扩展性 | 受限于 RM 质量 | 随环境并行度扩展 |
| 覆盖范围 | 通用 | 仅限可验证任务 |

### 局限性
RLVR 只在任务**可验证**时有效。对于开放式任务（创意写作、主观有用性），无法进行验证，仍需学得奖励模型。

## 关键论文

| 论文 | 年份 | 贡献 |
|------|------|------|
| [[luo-2025-gui-r1]] | 2025 | GUI-R1：RLVR + GRPO；3K 样本 → SOTA；涌现推理。 |
| [[lu-2025-ui-r1]] | 2025 | UI-R1：基于规则的 RLVR + DAST。 |
| [[bai-2024-digiirl]] | 2024 | DigiRL：可验证 device-control 奖励；offline-to-online RL。 |
| [[qi-2024-webrl]] | 2024 | WebRL：outcome-supervised RM + curriculum 作为 web agent RLVR。 |
| [[lai-2025-computerrl]] | 2025 | ComputerRL：API-GUI hybrid actions 的端到端 RLVR。 |

## 与相关概念的对比

|               | RLVR                        | [[reinforcement-learning-from-human-feedback]] | 基于规则的奖励           |
|---------------|-----------------------------|------------------------------------------------|--------------------------|
| 奖励来源       | 环境副作用                  | 学得的人类偏好模型                              | 手工设计的启发式规则      |
| 可作弊？       | 极低                        | 是（[[reward-hacking]]）                       | 中等                     |
| 覆盖范围       | 仅限可验证任务              | 通用                                            | 仅限预定义规则           |
| 可扩展性       | 高（环境并行）              | 有限（偏好数据）                                | 中等                     |

## 当前研究前沿
- **可验证复合奖励**：结合 URL 检查、DOM diff、文件状态检查用于长时程任务。
- **过程级 RLVR**：验证中间子目标而非仅最终结果。
- **不可验证任务的 RLVR**：如何把 RLVR 范式扩展到主观任务。
- **Sim-to-real**：模拟器中的 RLVR 信号；向真实环境的迁移。

## 参见
- [[gui-agent]]
- [[group-relative-policy-optimization]]
- [[reward-hacking]]
- [[reinforcement-learning-from-human-feedback]]
- [[multi-turn-gui-rl]]
