---
title: "Reward Variance"
aliases: ["RM Reward Variance", "On-Policy Reward Variance", "奖励方差"]
domain: reward-modeling
tags: [reward-modeling, rlhf, theory, policy-optimization, alignment]
source_count: 1
last_updated: 2026-04-27
---

# 奖励方差（Reward Variance）

## 定义
给定策略 $\pi_\theta$、prompt $x$、奖励模型
$r_\text{RM}: \mathcal X \times \mathcal Y \to [-1, 1]$，
**$r_\text{RM}$ 对策略 $\pi_\theta$ 诱导出的奖励方差**定义为：

$$
\mathrm{Var}_{y\sim\pi_\theta(\cdot|x)}[r_\text{RM}(x,y)]
\;=\; \mathbb E_{y\sim\pi_\theta(\cdot|x)}\!\left[\!\left(r_\text{RM}(x,y) - \mathbb E_{y'\sim\pi_\theta}[r_\text{RM}(x,y')]\right)^{\!2}\right].
$$

等价地，
$\tfrac12 \mathbb E_{y,y' \sim \pi_\theta}\big[(r_\text{RM}(x,y)-r_\text{RM}(x,y'))^2\big]$
—— 它就是**两个 on-policy 采样**之间奖励的**均方分离度**。

注意这与 *gradient variance*（RL 文献中常说的"采样级方差"）**不是
同一个概念**。

## 直觉
**准确率**衡量 RM 是否把输出**排得对**。奖励方差则衡量 ——
在*当前策略*的分布之下 —— RM 给输出之间留下了**多大的间隔**。

一个 RM 完全可以排序完美，但把所有可能输出压缩到几乎同一个分数 ——
这是一个"在策略侧扁平"的打分函数。这时策略梯度看到的是平坦地形，
更新几乎为零。提升奖励方差 = 告诉策略梯度*往哪个方向*、*多大力度*
去更新。

## 历史背景
- **2024** —— Razin et al., *Vanishing Gradients in Reinforcement
  Fine-tuning of Language Models*，证明对 softmax 策略，
  $\|\nabla \mathbb E_{y\sim\pi}[r(y)]\|$ 的尺度由
  $\mathrm{Var}_{y\sim\pi}[r(y)]$ 决定。低方差 ⇒ 梯度消失。
- **2025** —— [[razin-2025-good-teacher]] 在此基础上证明：低奖励方差
  ⇒ 奖励最大化*可证明地*缓慢，并由此论证**RM 评估应当是策略条件
  的**，并应在准确率之外加入方差度量。

## 关键变体 / 子类型

- **On-policy 奖励方差** —— 在**当前**策略采样的输出上度量的方差
  （与 RLHF 最相关）。
- **Off-policy 奖励方差** —— 在固定输出分布（如某个 benchmark
  数据集）上度量的方差。可能与 on-policy 偏离很大，会误导 RM 选型。
- **初始策略奖励方差** —— 在 SFT 检查点 $\pi_{\theta(0)}$ 上度量的
  方差。这是 [[razin-2025-good-teacher]] 中理论界使用的版本；在做 RL
  之前就能廉价计算。
- **逐 prompt vs 平均** —— 方差既可以是逐 prompt 的
  $\mathrm{Var}_{y\sim\pi(\cdot|x)}[r(x,y)]$，也可以是在训练 prompt
  集上平均的 $\mathbb E_{x\sim S}[\cdot]$。

## 技术细节

### 与梯度范数的关系

对一般的、通过 softmax 产生 next-token 分布的自回归策略
$\pi_\theta$，Razin et al. (2024) 证明

$$
\bigl\|\nabla_\theta\, \mathbb E_{y\sim\pi_\theta(\cdot|x)}[r(x,y)]\bigr\| \;\le\; C \sqrt{\mathrm{Var}_{y\sim\pi_\theta(\cdot|x)}[r(x,y)]},
$$

其中 $C$ 依赖于网络的 Jacobian。因此，*低方差 ⇒ 梯度消失 ⇒ 策略
改进缓慢*。

### Time-to-γ 下界

在 RLHF 目标的梯度流之下，期望奖励（无论代理 $r$ 还是真实 $r_G$）
增长 $\gamma$ 所需的时间为

$$
t_\gamma \;=\; \Omega\!\left(\Big(\mathbb E_{x\sim S}\!\big[\mathrm{Var}_{y\sim\pi_{\theta(0)}}[r_\text{RM}(x,y)]\big]\Big)^{-\tfrac13}\right)
$$

（[[razin-2025-good-teacher]] 定理 1）。关键是这个下界**与准确率
完全无关** —— 所以一个准确率完美但方差低的 RM 可以比一个方差高
但准确率差的 RM 慢任意倍。

### 为什么"乘个常数"治不了
把所有奖励乘以 $c$ 会让方差按 $c^2$ 放大，但同时会按比例放大梯度
估计的噪声。经验上（Razin 2025 §6.1），简单缩放*并不能*恢复快速学习。

## 关键论文

| 论文 | 年份 | 贡献 |
|------|------|------|
| [[razin-2025-good-teacher]] | 2025 | 把奖励方差形式化为 RM 质量缺失的关键维度；证明准确率不等于教学有效性；RM 质量的策略依赖性。 |

*（待摄取：Razin et al. 2024 — Vanishing Gradients in RL Fine-tuning；
Gao et al. 2023 — Scaling Laws for RM Over-Optimization。）*

## 与相关概念的对比

|                         | 奖励方差 Reward Variance     | 准确率 Accuracy             | 梯度方差 Gradient Variance |
|-------------------------|------------------------------|-----------------------------|---------------------------|
| 度量的对象              | *On-policy* 输出之间的分离度 | 成对排序的正确率            | 随机 $\hat\nabla$ 的噪声  |
| 是否依赖策略？          | **是**                       | 通常否（数据集级）          | 是（依赖采样数）          |
| 对 RLHF 的影响          | 控制*地形平坦度*             | 控制*与 $r_G$ 的对齐度*     | 控制*估计器噪声*          |
| 怎么测                  | 两个 on-policy 样本估计      | RewardBench 风格基准        | rollout 标准差            |
| 容易改善吗？            | 否（需要 RM 重新设计）       | 是（更多 / 更好的数据）     | 是（更多 rollout）        |

## 当前研究前沿

- **方差感知的 RM 训练** —— margin loss（如 Wang et al. 2023, Park
  et al. 2024）和 on-policy 偏好数据是天然能抬高方差的手段。
- **联合"方差 × 准确率"基准** —— 策略条件的 RM 打分协议。
- **RLHF 期间的方差动力学** —— 随策略漂移，方差会塌缩吗？多快？
- **与 [[generative-reward-model]] 的相互作用** —— 长 CoT GenRM 输出
  连续偏好强度，其方差谱是否系统地不同于标量 BT RM 尚不清楚。
- **可验证奖励** —— Razin 的分析能扩展到基于规则的 RL；当奖励是
  $\{0, 1\}$（数学/代码）时方差是怎么样？

## 参见
- [[razin-2025-good-teacher]]
- [[reward-model-as-teacher]]
- [[reinforcement-learning-from-human-feedback]]
- [[reward-hacking]]
- [[group-relative-policy-optimization]]
