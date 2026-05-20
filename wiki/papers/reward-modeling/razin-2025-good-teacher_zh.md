---
title: "What Makes a Reward Model a Good Teacher? An Optimization Perspective"
slug: "razin-2025-good-teacher"
authors:
  - "Noam Razin"
  - "Zixuan Wang"
  - "Hubert Strauss"
  - "Stanley Wei"
  - "Jason D. Lee"
  - "Sanjeev Arora"
year: 2025
venue: "NeurIPS 2025"
arxiv: "2503.15477"
domain: reward-modeling
tags: [reward-modeling, rlhf, alignment, theory, reward-variance, ppo, grpo, policy-optimization]
status: read
ingested: 2026-04-27
---

# 怎样的奖励模型才是好"老师"？—— 一个优化视角

## TL;DR
> RM 的**准确率**还不够。一个准确率完美的 RM，如果对初始策略诱导出
> **低奖励方差**，会让 [[reinforcement-learning-from-human-feedback]]
> 的优化地形变平 —— 让策略梯度爬行不前。一个准确率较差但奖励方差
> 高的 RM 反而是**更好的老师**。因此 RM 质量是**策略依赖**的，单靠
> 策略无关的基准衡量不出来。

## Problem Statement（问题陈述）
RLHF 的成败靠 RM，但 RM 质量几乎只通过**偏好基准上的准确率**
（如 RewardBench、RM-Bench）来衡量。最近的经验研究观察到：*RM 越准
不一定带来对齐越好的策略* —— 但一直没有理论解释。本论文要回答的：

1. *准确率之外*还有什么因素决定 RLHF 优化能否前进？
2. 一个准确率完美的 RM 也可能是个*坏老师*吗？
3. 同一个 RM 对不同的底座 LLM 会一好一坏吗？

## Key Contributions（主要贡献）
1. **找到了缺失的关键因素：reward variance（奖励方差）。**
   形式化定义 $\mathrm{Var}_{y\sim\pi_\theta(\cdot|x)}[r_\text{RM}(x,y)]$
   —— RM 对*策略真实采样到*的输出之间分得有多开。
2. **定理 1** —— 低奖励方差 ⇒ RLHF 目标的地形平坦 ⇒ *可证明的*奖励
   最大化缓慢，对一般自回归策略也成立（不只是表式策略）。把期望
   奖励抬高 $\gamma$ 所需时间为 $\Omega(\text{Var}^{-1/3})$。
3. **定理 2** —— 一个*完美准确*的 RM 可以**任意倍**地差于一个*几乎
   完全错误*的 RM，纯粹因为奖励方差差异。
4. **定理 3** —— 同一个 RM 可以对策略 A 是最好的老师、对策略 B 是
   最差的老师。**RM 质量与策略联合存在。**
5. **8B 规模的实验**（Pythia、Llama-3.2；RLOO + GRPO；AlpacaFarm +
   UltraFeedback）证实了理论：奖励方差与奖励最大化速率的相关系数
   r = 0.98；而准确率单看在某些场景下负相关。
6. **令人惊讶的实证发现**：即便 ground-truth 奖励是可访问的，
   *用代理 RM 训练有时也比直接用 ground-truth 训练更好* —— 因为代理
   RM 诱导了更高的奖励方差。

## Method Overview（方法概述）

这是一篇**理论 + 实证**论文，不是算法论文。核心概念性步骤：

### 关键定义

**准确率**（定义 1）：
$$
\mathrm{acc}_{x,\mathcal D}(r_\text{RM})
= \mathbb E_{\{y,y'\}\sim\mathcal D}\!\left[\mathbf 1\!\left[\text{sign}(r_\text{RM}(x,y)-r_\text{RM}(x,y'))=\text{sign}(r_G(x,y)-r_G(x,y'))\right]\right].
$$
只依赖**排序**，不依赖分离*程度*。

**奖励方差**（定义 2）：
$$
\mathrm{Var}_{y\sim\pi_\theta(\cdot|x)}[r_\text{RM}(x,y)]
= \tfrac12 \mathbb E_{y,y'\sim\pi_\theta(\cdot|x)}\!\left[(r_\text{RM}(x,y) - r_\text{RM}(x,y'))^2\right].
$$
依赖**分离多大**，并且依赖**策略实际产生的输出** —— 因此天然*依赖
策略*。

### 核心定理（定理 1，简化版）

设 $\phi_\text{RLHF}(\theta) = \mathbb E_x \big[\mathbb E_{y\sim\pi_\theta(\cdot|x)}[r_\text{RM}(x,y)] - \lambda\, \mathrm{KL}(\pi_\theta \Vert \pi_\text{ref})\big]$。
在梯度流之下，任意奖励 $r$（包括 $r_G$）增长 $\gamma$ 的时间为

$$
t_\gamma = \Omega\!\left(\Big(\mathbb E_{x\sim S}\!\big[\mathrm{Var}_{y\sim\pi_{\theta(0)}}[r_\text{RM}(x,y)]\big]\Big)^{-\tfrac13}\right),
$$

其中 $\Omega(\cdot)$ 隐藏了对 $\gamma, \lambda, L$ 以及策略网络
Jacobian 的依赖。**证明思路：** 低奖励方差不仅杀死
$\lVert\nabla\phi_\text{RLHF}\rVert$，还杀死所有高阶导数 —— 所以训练
中梯度范数也涨不上来 —— 由此得到对 $t_\gamma$ 的紧下界。

### 两个 准确率 vs 方差 的可分离构造（定理 2 & 3）

构造性的：明确给出表式策略下的例子，让一个完美准确率 $r_\text{RM}$
任意慢，而一个几乎完全错误的 $r'_\text{RM}$ 达到 $t_\gamma = O(1)$。
对定理 3，构造了两个具有匹配 $r_G$ 期望的策略族 $\Pi, \Pi'$，使得
$r_\text{RM}$ 把 $\Pi$ 教得很好、把 $\Pi'$ 教得很差，而 $r'_\text{RM}$
正好相反。

## Experiments & Results（实验与结果）

**设置。** Ground-truth 奖励由 ArmoRM-8B 模拟。策略底座 Pythia-2.8B
（消融加 Pythia-1B、Llama-3.2-1B/3B）。UltraFeedback prompt，80/20
切分用于 RM 训练 vs RL。默认用 RLOO，消融用 GRPO。

**RM 通过改变 RM 训练数据中 on-policy 偏好对的比例**（100%、75%、
50%、25%、0%）来产生**奖励方差差异**，加上一个合成的"完美准确率
但低方差"的 control。

### RM 的属性（Pythia-2.8B 初始；奖励已归一化）

| RM | 100% on-policy | 75% | 50% | 25% | 0% | 完美准/低方差 | 真实奖励 |
|----|----------------|-----|-----|-----|----|----|----|
| 奖励方差 | **0.630** | 0.616 | 0.555 | 0.438 | 0.314 | 0.111 | 0.256 |
| On-policy 准确率 | 0.660 | 0.656 | 0.640 | 0.616 | 0.587 | **1.000** | **1.000** |
| Off-policy 准确率 | 0.630 | 0.732 | 0.758 | 0.754 | 0.762 | **1.000** | **1.000** |

### 与 1 epoch 后奖励增量的相关性（表 2）

| 度量 | Pearson（代理 ↑）| Pearson（GT ↑）|
|------|------------------|----------------|
| 奖励方差 | **0.982** | **0.834** |
| On-policy 准确率 | −0.762 | −0.283 |
| Off-policy 准确率 | −0.933 | −0.507 |
| 奖励方差 × 准确率 | 0.676 | **0.940** |

### 关键质性发现

- "完美准确率、低方差"的 RM 在真实奖励增长上*跑不赢*所有 5 个
  "更糙但更高方差"的 RM。
- 当代理 RM 在 on-policy 下诱导了更高方差时，它在前几个 epoch
  **甚至跑赢 ground-truth 奖励**。
- 跨 3 个底座 × 4 个公开 RM，**最佳 RM 因底座而异**（定理 3 经验
  确认）。

## Strengths（优点）

- **理论严谨、实证扎实。** 在对齐工作中难得的组合：形式化
  $\Omega$/$O$ 上下界 + 8B 规模实验，且预测被定量复现（Pearson > 0.98）。
- **可操作的诊断。** 奖励方差能直接从初始策略上廉价测量出来 —— 给了
  实践者一个*RL 之前*的筛子，能预判某 RM 是否会成为一个有用的老师。
- **重构了 RM 评估视角。** "RM 即教师，而非分类器"是一次干净的
  概念性提炼，可以推广到知识蒸馏等其它师生场景。
- **能扩展到可验证奖励。** 分析与奖励来源无关，能直接套到基于规则
  的 RL（如带数学/代码奖励的 GRPO）—— 这一日益重要的场景。
- **指出了具体的缓解轴。** 如果低方差是病根，那么*基于 margin 的 RM
  目标*与 on-policy 偏好数据就成了天然解药。

## Limitations & Weaknesses（局限 / 不足）

- **定理 2 & 3 的表式策略假设。** 最惊艳的可分离结果（完美准确率
  RM 任意差于近随机 RM）只对表式策略成立；只有定理 1 推广到神经
  自回归策略。把这一缝隙补上是开放问题。
- **梯度流 ≠ 实战 SGD。** 小学习率极限的连续时间分析。随机梯度、
  mini-batch、clipping（PPO/GRPO）的实战动力学没有直接覆盖。
- **Ground truth 由另一个 RM（ArmoRM）模拟。** 如果 ArmoRM 自身有
  偏置，"方差胜过准确率"的发现会部分继承之。作者也指出这点；做
  人评复现会让故事更扎实。
- **规模止步 8B。** Pythia-2.8B + Llama-3.2 ≤ 3B 底座；RM 至多 8B。
  扩展到前沿规模时方差 / 准确率的 Pareto 可能改变。
- **没有给出建设性的修法。** 论文证明低方差致命，但没有提出能在
  *不损害准确率*的前提下抬高方差的具体 RM 训练目标。简单的奖励缩放
  被证明不行。
- **Best-of-N 只是侧面提及，没有展开。** RLHF（准确率不够）与
  Best-of-N（准确率最优）之间的不对称被点到，但没有充分展开。

## Open Questions（开放性问题）
- 我们应当如何**重新设计 RM 训练**才能*同时*最大化准确率与奖励方差
  （margin-based loss、on-policy 数据、集成）？
- 训练**期间**奖励方差的演化如何 —— 不只是 $t=0$ 时？随着策略漂离
  manifold，它会塌缩吗？
- 能否在这一框架下分析 **[[generative-reward-model]]**（如
  [[hong-2025-think-rm]]）？它们的长 CoT 输出大概率给出与标量 BT RM
  不同的方差谱。
- 在 **[[group-relative-policy-optimization]]** 之下，方差 / 准确率
  的取舍是怎样 —— GRPO 的组均值基线会改变这幅图吗？
- 奖励方差能预测多 epoch 之后的**端策略质量**，还是只能预测短时程
  优化速率？
- **Best-of-N vs RLHF**：论文说准确率对 Best-of-N 充分、对 RLHF 不
  充分。那 DPO、rejection sampling、constitutional alignment 呢？

## Connections（关联）
- **Builds on：** [[reinforcement-learning-from-human-feedback]]
  （它分析的对象是这个管线）；Razin et al. 2024（定理 1 援引的
  "vanishing-gradient / 奖励方差"连接）。
- **Motivated by：** [[reward-hacking]] —— 把奖励方差作为与"准确率
  hacking 取舍"正交的一个*额外*调节量提出来。
- **紧密相关（也是被对照的）：** [[hong-2025-think-rm]] —— 都问
  "什么算好 RM"，但角度互补：Think-RM 提升 RM 的*判断质量*；本文
  说**即便判断完美的 RM 也可能教不动**。详见对比页
  [[think-rm-vs-good-teacher]]。
- **Related concepts：** [[group-relative-policy-optimization]]
  （在附录 D.2 用到）、PPO、RLOO、reward shaping。
- **Extended by：** *（随 wiki 增长填入）*
- **同作者其他工作（本 wiki 内）：** *(尚无)*

## Personal Notes（个人笔记）
- 论文最值得引用的一句是 ——"*regardless of how accurate a reward
  model is, if it induces low reward variance, then the RLHF objective
  suffers from a flat landscape*" —— 它有一种 Goodhart 法则的反向
  味道：在没有方差成分的情况下最大化代理（准确率），优化的是
  奖励-偏好分布的*错误统计量*。
- 这个结果与 [[hong-2025-think-rm]] 配得很妙：那篇*提升* RM 准确率
  与判断深度；本篇说*没有方差的准确率不充分*。自然综合：用一个长
  CoT GenRM 同时配上 margin-augmented 训练、用面向目标策略的校准
  分布提升方差。我打赌这个组合就是 SOTA。
- 一份实战配方就此浮现：在跑 RLHF 之前，从 SFT 策略对每个 prompt
  采样 $G \ge 8$ 条 rollout，用候选 RM 打分，计算经验奖励方差，
  把方差低于阈值的 RM 直接淘汰 —— 不论 RewardBench 分数多高。
  本质上就是在准确率基准之上加一个"方差筛"。
- Best-of-N 的反例是个礼物 —— 它暗示一个一般原则：**对齐方法对
  RM"分布行为" vs "排序行为"的依赖度，决定了方差是否要紧**。这一
  二分法值得拥有自己的框架。

## BibTeX
```bibtex
@inproceedings{razin2025goodteacher,
  title     = {What Makes a Reward Model a Good Teacher? An Optimization Perspective},
  author    = {Razin, Noam and Wang, Zixuan and Strauss, Hubert and Wei, Stanley and Lee, Jason D. and Arora, Sanjeev},
  booktitle = {Advances in Neural Information Processing Systems (NeurIPS)},
  year      = {2025},
  url       = {https://arxiv.org/abs/2503.15477}
}
```
