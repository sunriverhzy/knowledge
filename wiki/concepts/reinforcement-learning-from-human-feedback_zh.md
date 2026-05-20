---
title: "Reinforcement Learning from Human Feedback"
aliases: ["RLHF", "基于人类反馈的强化学习"]
domain: llm
tags: [rlhf, alignment, rl, llm, reward-modeling]
source_count: 2
last_updated: 2026-04-27
---

# 基于人类反馈的强化学习（RLHF）

## 定义
**基于人类反馈的强化学习（RLHF）** 是一种针对大语言模型的**后训练**
范式：策略 $P_\phi$ 被优化以最大化一个**学得的**奖励信号 $r$，而该
奖励的最优解反映了人类偏好：

$$
\max_\phi \;\mathbb E_{x\sim\mathcal D,\; y\sim P_\phi(\cdot\mid x)}\big[ r(x,y) \big]
\;-\; \beta\, D_{\text{KL}}(P_\phi \Vert P_{\phi_\text{ref}}).
$$

奖励 $r$ 通常由一个在"成对回答的人类偏好对比"上训练得到的
**奖励模型（RM）**给出。

## 直觉
预训练优化的是文本上的似然 —— 而不是**有用性**或**安全性**。RLHF
弥合了这个鸿沟：**让人类（或其学到的代理）给模型输出打分**，再把
这些打分变成模型可遵循的可微目标。一个对预训练参考的 KL 惩罚则
防止模型坍缩到一个狭窄的、奖励作弊的模式。

## 历史背景
- **2017** —— Christiano et al. 提出 *Deep RL from Human Preferences*，
  在仿真机器人任务上确立了"偏好学习 + PPO"的基本配方。
- **2020** —— Stiennon et al. 把 RLHF 应用到 GPT-3 的**摘要任务**，
  第一次给出了 LLM 规模的 RLHF 演示。
- **2022** —— Ouyang et al., *InstructGPT*，把 RLHF 扩展到通用指令
  跟随 —— 这一模板被 ChatGPT 以及之后所有主流对齐 LLM 沿用。
- **2023–2024** —— 出现一波替代与改进：**DPO**（Rafailov 2023）通过
  重参数化偏好目标，去掉了显式的奖励模型；**RLAIF** 用 AI 标签替换
  人类标签；**Constitutional AI** 使用基于原则的自我批判。
- **2024–2025** —— 奖励建模本身成为研究主题：[[reward-hacking]]
  的诊断、[[generative-reward-model]]、过程奖励模型，以及针对推理
  任务的基于规则的可验证奖励（如 DeepSeek-R1 + GRPO）。

## 关键变体 / 子类型

- **经典 RLHF（PPO + BT RM）** —— Ouyang 2022。BT 奖励模型 + 带
  KL 惩罚的近端策略优化。事实上的默认。
- **DPO（直接偏好优化）** —— Rafailov 2023。去掉 RL 循环；LLM
  *本身*就是（隐式的）奖励模型。
- **GRPO（分组相对策略优化）** —— Shao/Guo et al.（DeepSeek）。
  通过分组归一化省掉价值函数；在推理 RL 中流行。见
  [[group-relative-policy-optimization]]。
- **RLAIF / Constitutional AI** —— 用 LLM 评判者代替人类标签
  （Bai et al.）。
- **成对 RLHF（搭配 GenRM）** —— [[hong-2025-think-rm]]。RLHF 端到
  端保持成对，无需点态奖励转换。
- **基于规则的 / 可验证 RL** —— 当存在 ground-truth 答案（数学、
  代码）时直接用作奖励（DeepSeek-R1 风格）。常被称为"基于可验证
  奖励的 RL"，而不是 RLHF。

## 技术细节

### 标准 PPO-RLHF 管线
1. **SFT**：在高质量 demonstration 上做监督微调。
2. **RM 训练** —— 在人类偏好对 $(y_w \succ y_l \mid x)$ 上拟合
   Bradley–Terry 模型，最小化
   $-\mathbb E[\log \sigma(r(x,y_w) - r(x,y_l))]$。
3. **PPO 优化** —— 在 $r$ 之上优化策略，加上对 $\phi_\text{ref}$ 的
   逐 token KL 惩罚。

### PPO 代理目标（用于 RLHF）
$$
L_\text{CLIP}(\phi) = \mathbb E_t\!\left[ \min\!\left(\rho_t \hat A_t,\;
\text{clip}(\rho_t, 1{-}\epsilon, 1{+}\epsilon) \hat A_t\right)\right]
\;-\; \beta\, D_\text{KL}(P_\phi \Vert P_{\phi_\text{ref}}).
$$

### Advantage 估计
要么用 [GAE](https://arxiv.org/abs/1506.02438)（需要价值函数），
要么用 [[group-relative-policy-optimization]]（不需要价值函数，
分组归一化奖励）。

## 关键论文

| 论文 | 年份 | 贡献 |
|------|------|------|
| [[hong-2025-think-rm]] | 2025 | 面向 GenRM 的成对 RLHF 管线 —— 完全省掉点态奖励转换。 |
| [[razin-2025-good-teacher]] | 2025 | RLHF 目标的理论分析：低奖励方差 ⇒ 平坦地形 ⇒ 奖励最大化变慢，**与 RM 准确率无关**。RM 质量是策略依赖的。 |

*（待摄取的奠基论文：Christiano 2017；Stiennon 2020；Ouyang 2022 —
InstructGPT；Rafailov 2023 — DPO；Guo 2025 — DeepSeek-R1。）*

## 与相关概念的对比

|                | RLHF (PPO + BT RM)  | DPO                     | 基于规则的 RL（可验证奖励） |
|----------------|---------------------|-------------------------|--------------------------|
| 需要 RM？      | 是                  | 否（隐式）              | 否（需要 verifier / 规则）|
| 在线 / 离线？  | 在线                | 离线                    | 在线                     |
| 奖励来源       | 学得的 RM           | 偏好对                  | Ground-truth verifier    |
| 失败模式       | [[reward-hacking]]  | 分布坍缩                | Specification gaming     |
| 最适用于       | 开放任务            | 偏好数据丰富时          | 数学 / 代码 / 可验证任务 |

## 当前研究前沿
- **更好的奖励模型** —— [[generative-reward-model]]、过程奖励、
  长时程推理 RM。
- **"作为教师的 RM"框架** —— 见 [[reward-model-as-teacher]] 和
  [[reward-variance]]；准确率基准对 RM 质量描述不充分，RM 评估应该
  是策略条件的（[[razin-2025-good-teacher]]）。
- **没有人类的 RLHF** —— RLAIF、Constitutional AI。
- **不可验证任务** —— 如何把基于规则的 RL 想法（GRPO 风格）应用到
  数学/代码之外。
- **多奖励聚合** —— 多 RM 微调的机制设计。
- **长时程 / Agent RLHF** —— 多轮 Agent 的轨迹级奖励，而不是单回答
  奖励。

## 参见
- [[generative-reward-model]]
- [[group-relative-policy-optimization]]
- [[reward-hacking]]
- [[reward-variance]]
- [[reward-model-as-teacher]]
- [[chain-of-thought]]
