---
title: "Group Relative Policy Optimization"
aliases: ["GRPO", "分组相对策略优化"]
domain: rl
tags: [grpo, rl, rlhf, ppo, llm, policy-optimization]
source_count: 2
last_updated: 2026-04-27
---

# 分组相对策略优化（GRPO）

## 定义
**分组相对策略优化（GRPO）** 是一种策略梯度 RL 算法 ——
[PPO](https://arxiv.org/abs/1707.06347) 的一个变体 —— 它把学得的
价值函数（critic）替换成了**按组归一化的奖励基线**。对每个 prompt
采样 $G$ 次 rollout，每条 rollout 的 advantage 通过用组内均值与标准差
做标准化得到：

$$
\hat A_i \;=\; \frac{r_i - \bar r}{\hat\sigma_r + \epsilon},
\qquad
\bar r = \tfrac{1}{G}\sum_j r_j, \;\;
\hat\sigma_r = \sqrt{\tfrac{1}{G-1}\sum_j (r_j - \bar r)^2}.
$$

代理损失在其它方面与 PPO 相同（裁剪比 + 对参考的 KL 惩罚）：

$$
L_\text{GRPO}(\theta) = \mathbb E\!\left[\tfrac1G\sum_i \min\!\left(\rho_i \hat A_i,\; \text{clip}(\rho_i, 1{-}\epsilon, 1{+}\epsilon) \hat A_i\right)\right] - \beta\, D_\text{KL}(\pi_\theta \Vert \pi_\text{ref}).
$$

## 直觉
为 LLM 规模的 RL 训练 critic 是：
- **昂贵的**（多一个与策略同等大的模型），并且
- **脆弱的**（长序列上的 value target 噪声很大）。

GRPO 注意到：既然反正每个 prompt 都要采 $G$ 条轨迹，那么**组均值
本身可以被当作零成本的基线**，组标准差则可作为奖励缩放器。这样就
完全去掉了 critic，把显存约占少 ~2×、并简化了训练循环 —— 在 LLM
规模下是个大胜利。

## 历史背景
- **PPO（Schulman 2017）** —— RLHF 默认算法，带独立的 value head /
  critic 网络。
- **GRPO（DeepSeek 团队，2024）** —— 首次出现在 *DeepSeekMath*，因
  **DeepSeek-R1**（Guo et al. 2025）成名 —— 后者把它作为带可验证奖励
  的"基于规则的 RL"在数学 / 代码任务上的引擎。
- **2025** —— 在 LLM 推理 RL 中被广泛采用：
  - QwQ-32B（Qwen Team）
  - 各种 o 系列复现
  - 奖励模型训练（如 [[hong-2025-think-rm]] 同时把 GRPO 用于 RM
    精修和下游成对 RLHF）。

## 关键变体 / 子类型

- **原始 GRPO** —— 组内标准化奖励 + 对参考的 KL。
- **带可验证奖励的 GRPO** —— $r \in \{0, 1\}$（或经过 shaping）；
  数学/代码推理 RL 的标准配置。
- **成对 GRPO**（[[hong-2025-think-rm]]）—— advantage 由斜对称偏好
  矩阵 $D$ 计算，而不是标量奖励；在 $r_i - \bar r = \tfrac{1}{G}\sum_j d_{ij}$
  这一恒等式下与原版解析等价。
- **带过程奖励的变体** —— 逐步计算 GRPO advantage（活跃研究方向）。
- **Leave-one-out 基线（RLOO）** —— 紧密相关；用*其它*rollout 的均值
  代替包含 $r_i$ 自身的均值。

## 技术细节

### 成对等价（来自 [[hong-2025-think-rm]]）
给定对同一 prompt 的回答 $y_1, \dots, y_G$，以及一个产生偏好强度
$d_{ij} = -s_{ij}$（斜对称）的成对评判者，定义

$$
\hat A_i = \frac{\sum_{j=1}^G d_{ij}}{\sqrt{\tfrac{G}{2(G-1)} \sum_{i,j} d_{ij}^2 \;+\; G\epsilon}}.
$$

利用 $r_i - \bar r = \tfrac1G \sum_j (r_i - r_j) = \tfrac1G \sum_j d_{ij}$
这一恒等式以及对 $\hat\sigma_r$ 的相应改写，这正好恢复了标准 GRPO
的 advantage —— **而无需具象化任何点态标量奖励 $r_i$**。这让 GRPO
天然兼容成对 [[generative-reward-model]]。

### 为什么不要 critic？
设 $V_\phi$ 是 PPO 中的价值函数，其作用是作为基线降低方差。在 PPO
里 $\hat A_t = \hat Q_t - V_\phi(s_t)$。GRPO 把 $V_\phi$ 替换成 $\bar r$
（组内均值）。对单个 prompt 这是一个**有偏但一致**的基线（组均值
不等于条件价值），但跨 prompt 的偏差期望为零，方差缩减经验上够用。

## 关键论文

| 论文 | 年份 | 贡献 |
|------|------|------|
| [[hong-2025-think-rm]] | 2025 | 把 GRPO 扩展到成对奖励；证明与点态 GRPO 的等价性。 |
| [[razin-2025-good-teacher]] | 2025 | 在附录 D.2 中用 GRPO 验证"奖励方差控制优化速率"理论同样适用于 GRPO（与 RLOO / PPO 等价）。 |

*（待摄取：DeepSeekMath；DeepSeek-R1 — Guo 2025；Schulman 2017 — PPO。）*

## 与相关概念的对比

|                          | GRPO          | PPO + GAE            | RLOO                  | REINFORCE          |
|--------------------------|---------------|----------------------|-----------------------|--------------------|
| 需要价值函数？           | 否            | 是                   | 否                    | 否                 |
| 基线                     | 组内均值      | $V_\phi(s_t)$        | Leave-one-out 均值    | 无（或滑动均值）   |
| 每个 prompt 采样数       | $G \ge 2$     | 任意                 | $G \ge 2$             | 1+                 |
| 兼容可验证奖励？         | 原生兼容      | 兼容                 | 兼容                  | 兼容               |
| 额外显存                 | 低            | ~2×（value 网络）    | 低                    | 最低               |
| 典型用途                 | 推理 RL       | 通用 RLHF            | 廉价替代              | 简单基线           |

## 当前研究前沿
- **GRPO 在何时不如 PPO？** —— 关于"组基线何时足够"的理论刻画。
- **GRPO 内部的过程奖励** —— 逐步 advantage 计算。
- **成对 / 偏好原生 GRPO** —— 把 [[hong-2025-think-rm]] 的推导扩展到
  $K$-元偏好。
- **完全无 RM 的 GRPO** —— 用纯基于规则的可验证奖励（DeepSeek-R1
  风格）vs 与学得的 RM 混合。

## 参见
- [[reinforcement-learning-from-human-feedback]]
- [[generative-reward-model]]
- [[reward-variance]]
- [[reward-model-as-teacher]]
- [[chain-of-thought]]
