---
title: "Generative Reward Model"
aliases: ["GenRM", "Generative RM", "CoT-GenRM", "生成式奖励模型"]
domain: reward-modeling
tags: [reward-modeling, rlhf, chain-of-thought, generative-reward-model, llm-as-a-judge]
source_count: 1
last_updated: 2026-04-27
---

# 生成式奖励模型（Generative Reward Model）

## 定义
**生成式奖励模型（GenRM）** 是一种以 LLM 实现的奖励模型，其特征是
**在给出奖励或偏好标签之前，先以自然语言生成一段推理过程**。它不像
[Bradley–Terry 奖励模型](#参见)那样以判别式映射 $(x, y) \mapsto r \in \mathbb R$
作为输出，而是把任务因式分解为：

$$
(R, s) \sim G_\theta(\cdot \mid t, x, y_a, y_b),
$$

其中 $R$ 是生成的推理轨迹（一段 [[chain-of-thought]]），$s$ 是
偏好标签（成对：$s \in \{a, b\}$ 或带强度的序数标签），或一个
点态标量。

## 直觉
GenRM 实际上就是**作为评判者的 LLM** —— 但它是被训练来当评判者，
而不仅是被提示来当评判者。在给出判决前先生成一段 rationale，使得
GenRM 可以：(i) 调用预训练 LLM 自带的推理能力；(ii) 暴露出可解释的
"为什么 A 比 B 好"的痕迹；(iii) 利用**测试时算力**（多想一会儿 →
判得更准）—— 这是一个判别式打分头根本做不到的事。

## 历史背景
GenRM 范式来自三条线索的汇合：
1. **RLHF 的奖励建模** —— 自 Christiano 2017、Ouyang 2022 以来 BT RM
   一直是默认选择。
2. **LLM-as-a-Judge**（Zheng et al. 2023, *MT-Bench*）—— 表明*未经
   训练*的 LLM 可以充当评估者，但带有偏见。
3. **思维链提示** —— 表明带推理痕迹的输出能改进任务正确率。

最早的几篇明确的 GenRM 论文（如 Ankner et al. 2024 ——
*Critique-out-Loud*；Mahan et al. 2024 —— *Generative Reward Models*；
Zhang et al. 2025 —— *Generative Verifiers*）使用 next-token prediction
目标在 `(rationale, label)` 对上训练 LLM，并展示了比 BT RM 更强的
OOD 泛化能力。

## 关键变体 / 子类型

- **点态 GenRM** —— 对单个回答输出标量奖励。直接兼容点态 RLHF（PPO、GRPO）。
- **成对 GenRM** —— 对两个回答输出偏好（二值 $\{a, b\}$ 或带强度的
  序数）。需要点态转换（如 Elo 风格聚合），或如 [[hong-2025-think-rm]]
  那样使用专门设计的**成对 RLHF**管线。
- **带垂直推理时扩展的 CoT-GenRM** —— 独立采样 $m$ 条短 rationale，
  以多数投票聚合标签。提升自一致性，但在推理密集任务上会触顶。
- **长时程 GenRM**（"Think-RM"风格）—— 生成单条很深的、带自反思的
  rationale（约 1k–3k token）。是与"垂直"扩展相对的"水平"推理时
  扩展。
- **过程奖励 GenRM** —— 沿推理轨迹给出每一步奖励；与过程奖励模型
  （Lightman 2023；Wang 2023）相关。

## 技术细节

成对 GenRM（给定推理轨迹 $\hat R$）的 **SFT 目标**：

$$
L_{\text{SFT}}(\theta) = \mathbb E\big[-\log G_\theta(s \mid t,x,y_a,y_b, \hat R) - \log G_\theta(\hat R \mid t,x,y_a,y_b)\big].
$$

**基于规则的 RL 精修**（如：仅准确率奖励）：
$r = 1$ 当预测标签与真值一致，否则 $0$；用 GRPO 或 PPO 加上对参考
模型的 KL 正则。

**在 RLHF 中消费 GenRM 的输出**：
- *点态路径：* 直接作为 $r(x, y)$ 接入 PPO/GRPO。
- *成对路径：* 要么转成标量（如拟合一个 BT 分数），要么使用成对
  advantage 推导。[[hong-2025-think-rm]] 证明了在斜对称偏好矩阵下
  成对 GRPO 的 advantage 与点态 GRPO 在解析意义上完全等价。

## 关键论文

| 论文 | 年份 | 贡献 |
|------|------|------|
| [[hong-2025-think-rm]] | 2025 | GenRM 的水平推理时扩展；无需点态转换的成对 RLHF 管线；sub-10B 在 RM-Bench 上达到 SOTA。 |

*（待摄取：Mahan 2024 — Generative Reward Models；Ankner 2024 —
Critique-out-Loud；Zhang 2025 — Generative Verifiers；Yu 2024 —
Self-generated critiques。）*

## 与相关概念的对比

|                       | 生成式奖励模型 GenRM | Bradley–Terry RM | LLM-as-a-Judge（仅提示） |
|-----------------------|--------------------|------------------|------------------------|
| 是否需要训练？        | 是（SFT ± RL）     | 是（分类）       | 否                     |
| 输出                  | rationale + 标签/标量 | 标量          | 标签（rationale 可选） |
| 测试时算力扩展        | 良好（垂直/水平）  | N/A              | 通过提示扩展           |
| OOD 鲁棒性            | 高                 | 低               | 中（易有偏）           |
| 可解释性              | 高（带 rationale） | 无               | 高                     |
| 数据效率              | 高                 | 低               | N/A（zero-shot）       |
| 对 [[reward-hacking]] 的脆弱性 | 较低     | 高               | 偏置驱动的失败         |

## 当前研究前沿
- **水平推理时扩展** —— 单条很长的 rationale 在难任务上击败多条短
  rationale（见 [[hong-2025-think-rm]]）。
- **过程级 GenRM** —— 把 rationale 当作每步奖励信号。
- **不可验证任务** —— 当不存在 ground-truth 标签时（如创意写作、
  多准则有用性）该如何训练 GenRM。
- **成对 RLHF** —— 不通过点态转换直接消费成对 GenRM 的输出。
- **自生成 / 自批判**训练数据（Yu et al. 2024）。
- **超过 8B 的扩展** —— 几乎所有当前 GenRM 研究都被卡在 7–10B 规模，
  原因是成对 RLHF 的算力开销。

## 参见
- [[reinforcement-learning-from-human-feedback]]
- [[chain-of-thought]]
- [[reward-hacking]]
- [[group-relative-policy-optimization]]
