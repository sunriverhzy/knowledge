---
title: "Multi-Turn GUI RL"
aliases: ["多轮 GUI 强化学习", "M-GRPO", "Long-horizon GUI RL"]
domain: agent
tags: [agent, gui-agent, rl, grpo, multi-turn, long-horizon]
source_count: 8
last_updated: 2026-05-20
---

# 多轮 GUI RL（Multi-Turn GUI RL）

## 定义
**多轮 GUI RL** 是指优化 GUI / computer-use 任务**完整交互轨迹**的强化学习方法，而不是单个动作。一条 GUI 任务轨迹 $\tau = (o_0, a_0, o_1, a_1, \ldots, o_T, a_T, r_T)$ 通常包含 20–200+ 步；标准单步 RL 算法在如此长的时程下面临严重的 credit assignment 挑战。

多轮方法把轨迹当作优化单元，使用 Multi-Turn GRPO（M-GRPO）、层次化 advantage 估计或动态上下文压缩等技术来处理长时程 credit assignment。

## 直觉
只在第 200 步（表单提交时）给 GUI agent 奖励，就像训练下棋手只告诉他赢了还是输了，没有任何中间反馈。多轮 RL 提供了结构：要么把奖励分解为中间 credit，要么以整条轨迹为单位优化。

## 关键变体 / 子类型

### Multi-Turn GRPO（M-GRPO）
[[wei-2025-webagent-r1]] 提出：把完整轨迹 $\tau$ 当作优化样本，类似标准 GRPO 把单条回复当作优化单元。

$$
\hat A(\tau_i) = \frac{r(\tau_i) - \bar r}{\hat\sigma_r}, \quad \text{更新: } \max_\theta \sum_i \hat A(\tau_i) \sum_t \log \pi_\theta(a_t | o_t, H_t)
$$

这让 agent 能学习"延迟满足"——为 50 步后的成功铺路的早期动作。

### 层次化 RL
分离 **Planner**（生成语义子目标）和 **Executor**（执行原子 UI 动作）。Hi-Agent（`2510.14388`）通过 GRPO 和 Foresight Advantage Function 联合训练 Planner + Executor，在 AitW 上达到 87.9% 成功率。

### Credit assignment 策略

| 策略 | 机制 | 示例 |
|------|------|------|
| **稀疏终止奖励** | 仅在任务完成时 $r_T = 1$ | 标准 WebArena, OSWorld |
| **过程奖励** | 每个已验证子目标给奖励 | ProgRM, AgentPRM |
| **HIPER credit assignment** | 层次化 RL + 结构化奖励分解 | HIPER（`2602.16165`） |
| **M-GRPO 轨迹级** | 整条轨迹优化 | WebAgent-R1 |

### System 1 / System 2 双模式路由
一个反复出现的发现：推理（CoT）有助于规划，但会伤害精确 grounding。多轮系统越来越多地学习*何时*调用哪个模式：

- **System 1**（快速）：无 CoT 的直接坐标预测。
- **System 2**（慢速）：子任务分解、错误恢复、策略层面的推理。

[[liu-2026-infigui-g1]] 经验性地验证了这一点；路由函数本身是 RL 优化的目标。

## 关键论文

| 论文 | 年份 | 贡献 |
|------|------|------|
| [[wei-2025-webagent-r1]] | 2025 | M-GRPO：面向 web agent 的轨迹级多轮 RL。 |
| [[wu-2025-hi-agent]] | 2025 | 层次化 VLA：Planner + Executor + GRPO；AitW 87.9%。 |
| [[lu-2025-ui-s1]] | 2025 | Semi-online 轨迹 patching：无需真实 env 的多轮纠错模拟。 |
| [[bai-2024-digiirl]] | 2024 | Offline-to-online RL；指令级价值函数用于长时程任务。 |
| [[peng-2026-hiper]] | 2026 | HIPER：带结构化奖励分解的层次化 RL。 |

## 与相关概念的对比

|                       | 多轮 GUI RL                     | 单步 RL                         |
|-----------------------|---------------------------------|---------------------------------|
| 优化单元              | 完整轨迹 / 子目标               | 单个动作 token                   |
| Credit assignment     | 困难（延迟 = 20–200 步）         | 简单（延迟 = 1 步）              |
| 典型时程              | 20–200 步                       | 1 步                            |
| 所需基础设施          | 轨迹 rollout、记忆管理           | 标准 GRPO / PPO                 |

## 当前研究前沿
- **长时程 GUI 任务的过程奖励**：奖励子目标完成而非仅最终结果。
- **记忆压缩**：如何在 200+ 步的轨迹中保留任务相关上下文而不超出上下文窗口。
- **System 1 / System 2 学得路由**：何时规划、何时执行、何时验证。
- **安全的长时程探索**：如何避免在 200 步轨迹深处发生不可逆错误。

## 参见
- [[gui-agent]]
- [[reinforcement-learning-with-verifiable-rewards]]
- [[group-relative-policy-optimization]]
- [[gui-io-wall]]
- [[gui-world-model]]
