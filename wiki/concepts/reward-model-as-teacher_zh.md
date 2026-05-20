---
title: "Reward Model as Teacher"
aliases: ["RM as Teacher", "Teacher View of RMs", "RM-as-Teacher", "作为教师的奖励模型"]
domain: reward-modeling
tags: [reward-modeling, rlhf, alignment, theory, optimization]
source_count: 1
last_updated: 2026-04-27
---

# 作为"教师"的奖励模型（Reward Model as Teacher）

## 定义
**"奖励模型即教师"** 框架把奖励模型 $r_\text{RM}$ 看作*指导者*而不是
*分类器*：它的角色是通过策略梯度，**驱动一个学生策略 $\pi_\theta$
向更高的真实奖励 $r_G$ 改进**。在这个框架下，关于 RM 质量的正确
问题是 *"这个 RM 在**这一具体策略**上能多高效地教出更高的 $r_G$？"*
—— **而不是** *"这个 RM 在固定基准上排序对了多少？"*。

## 直觉
分类器的工作止于"A 比 B 好吗？"。教师的工作要求更高：教学内容必须
*学生在其当前状态下学得会*。一个完美的评判，如果给每个学生都打
95/100，其实什么也没教 —— 学生需要的是梯度，而不仅仅是排序。

准确率属于"评判者"框架；[[reward-variance]] 属于"教师"框架。

## 历史背景

- **2017–2022** —— 分类器框架占主导：BT 奖励模型、RewardBench 风格
  的准确率基准。隐含假设：RM 越准 ⇒ RLHF 后策略越对齐。
- **2023–2024** —— 经验上的裂缝：[Chen et al. 2024](#)、
  [Wen et al. 2024](#)、[Chen et al. 2025](#) 观察到 RewardBench
  分数更高的 RM *不一定*带来更好的策略。
- **2025** —— [[razin-2025-good-teacher]] 给出了第一个形式化的解释：
  缺失的成分是 [[reward-variance]]。该论文显式援引了"作为教师的 RM"
  框架，并把它与"分类器"框架的根本不同形式化下来。

## 关键变体 / 子类型

不是单一方法，而是一**簇视角与评估方案**：

- **策略条件的 RM 评估** —— 针对具体学生策略给 RM 打分，而不是
  通用打分。
- **on-policy 偏好评估** —— 用学生采样的输出（而非 off-policy
  benchmark 集）来度量准确率（与方差）。
- **方差感知的 RM 打分** —— 把
  $\mathbb E_x[\mathrm{Var}_{y\sim\pi}[r_\text{RM}]]$ 作为一等公民
  的质量指标（[[razin-2025-good-teacher]] 的论点）。
- **抬高方差的 RM 训练** —— margin loss、on-policy 偏好数据、
  以"提升方差"为目的的集成。
- **课程 / 自适应教师** —— 在不同训练阶段给学生用不同的 RM
  （先前工作中隐含使用；尚未很好形式化）。

## 技术细节

### 为什么准确率对 Best-of-N 够用、但对 RLHF 不够

对 Best-of-N 采样而言，RM 的角色*纯粹*是把 $N$ 个候选输出排序、
取第一名。可证完美准确率的 RM 是最优的（Razin 2025, 命题 1）。
没有"学习动态"可言 —— 只有排序。

对 RLHF（策略梯度）而言，RM 的角色是塑造给学生的**梯度信号**。
排序是必要但不充分的；梯度还必须**非消失**，也即奖励方差不能太低。

这种不对称性给出了一个有用的对齐方法分类：

| 方法                                | 依赖 RM **排序**？  | 依赖 RM **分离 / 方差**？ |
|-------------------------------------|---------------------|-------------------------|
| Best-of-N                           | 是                  | 否                       |
| Rejection sampling / RAFT           | 是                  | 否                       |
| RLHF（PPO / GRPO / RLOO）           | 是                  | **是**                   |
| DPO                                 | 是（隐式）          | 部分（loss 中含 margin） |
| Constitutional AI 自我批判          | 是                  | 不清楚                   |

### RLHF 之前的诊断检查清单

灵感来自 [[razin-2025-good-teacher]]：

1. 从 $\pi_{\theta(0)}$ 对每个 prompt 采样 $G \ge 8$ 条 rollout。
2. 用候选 RM 计算每个 prompt 的经验奖励方差。
3. 把平均方差低于阈值的 RM 直接淘汰，**不论** RewardBench 分数有多高。
4. 多个候选都通过时，再用准确率作为次要标准。

## 关键论文

| 论文 | 年份 | 贡献 |
|------|------|------|
| [[razin-2025-good-teacher]] | 2025 | 把"教师"框架形式化；证明对 RLHF 准确率不充分，但对 Best-of-N 准确率最优。 |

*（待摄取的相邻论文：Chen et al. 2024；Wen et al. 2024；Chen et al.
2025 —— 这些都经验性地观察到"准确的 RM ≠ 更好的策略"。）*

## 与相关概念的对比

|                   | 作为教师的 RM             | 作为分类器的 RM           | 作为 verifier 的 RM（可验证奖励） |
|-------------------|---------------------------|---------------------------|---------------------------------|
| RM 的目标         | 驱动策略改进              | 把成对排序排对             | 验证 ground-truth 正确性         |
| 评估              | 策略条件、方差感知        | 策略无关的准确率          | 与参考做精确匹配                 |
| 数据来源          | 通常 on-policy            | 通常 off-policy benchmark | Ground-truth 数据集              |
| [[reward-hacking]] 风险 | 高，但与方差有关     | 高，且与准确率有关        | 低（如果规则精确）               |

## 当前研究前沿

- **策略条件的 RM 基准** —— 一个 *RewardBench-2 教师版*，按底座模型
  族报告方差曲线。
- **抬高方差的 RM 训练** —— margin loss、on-policy 数据、集成。
- **教师动力学** —— RM 质量在 RLHF *期间*如何随学生漂移而演变；是否
  应该在训练中途换 RM。
- **GenRM 的教师框架** —— 长 CoT rationale 是系统地提高还是降低
  on-policy 方差？
- **联合"方差 × 准确率"目标** —— 当前 Pareto 前沿是经验拟合的，
  尚未被显式训练。

## 参见
- [[reward-variance]]
- [[reinforcement-learning-from-human-feedback]]
- [[reward-hacking]]
- [[generative-reward-model]]
- [[group-relative-policy-optimization]]
