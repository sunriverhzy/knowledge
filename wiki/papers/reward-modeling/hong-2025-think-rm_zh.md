---
title: "Think-RM: Enabling Long-Horizon Reasoning in Generative Reward Models"
slug: "hong-2025-think-rm"
authors:
  - "Ilgee Hong"
  - "Changlong Yu"
  - "Liang Qiu"
  - "Weixiang Yan"
  - "Zhenghao Xu"
  - "Haoming Jiang"
  - "Qingru Zhang"
  - "Qin Lu"
  - "Xin Liu"
  - "Chao Zhang"
  - "Tuo Zhao"
year: 2025
venue: "arXiv 2025（预印本，审稿中）"
arxiv: "2505.16265"
domain: reward-modeling
tags: [reward-modeling, rlhf, generative-reward-model, grpo, chain-of-thought, llm, alignment]
status: read
ingested: 2026-04-27
---

# Think-RM：让生成式奖励模型具备长时程推理能力

## TL;DR
> 与其*垂直地*扩展生成式奖励模型（采更多短推理 + 多数投票），不如
> **把它*水平地*扩展**：训练 LLM 在给出成对偏好之前先吐出**一条很长、
> 带自反思的思维链**，再用这条**成对**奖励**直接**驱动 RLHF（无需
> 转换成点态奖励）。

## Problem Statement（问题陈述）
当前 RLHF 奖励建模的两个痛点彼此关联：

1. **Bradley–Terry 奖励模型（BT RM）** —— RLHF 的主流奖励信号 ——
   数据饥饿、对分布偏移脆弱，且容易遭 [[reward-hacking]]。
2. **现有的 [[generative-reward-model]]（GenRM）** 通过在打分前生成
   [[chain-of-thought]] 部分缓解了这个问题，但每条 CoT 都*很浅*
   （几百 token）。为此实践中做**垂直推理时扩展** —— 采样多条 CoT
   做多数投票。但在推理密集的 RM 任务（代码、数学、多轮、微妙
   错误检测）上，这种做法触顶 —— 因为没有任何一条浅链足够深以
   解开判断。

第二，GenRM 输出**成对**偏好，而标准 RLHF（PPO / GRPO）期望**点态
**标量奖励。常用的"点态转换"步骤丢信息且数学上别扭。

## Key Contributions（主要贡献）
1. **Think-RM** —— 一个训练框架，把非推理型 LLM（Llama-3.1-8B-Instruct）
   变成产生 **1k–3k+ token** 单条推理轨迹的*长时程*推理 GenRM，
   该轨迹带有**自反思、反事实推理、发散性推理**。
2. **两阶段训练配方**：在长 CoT 数据上 SFT 预热（按"最长且正确"
   过滤）+ 仅准确率奖励的基于规则的
   [[group-relative-policy-optimization]] RL 精修。
3. **成对 RLHF 管线**直接消费 Think-RM 的成对输出 —— 无需点态
   转换。它构建一个斜对称偏好强度矩阵 $D$ 并解析地推出 GRPO
   advantage，与标准点态版本完全等价。
4. **SOTA**：在 RM-Bench 上以 6k 训练样本拿下 sub-10B 公开模型的
   SOTA（比 BT RM 与垂直扩展的 CoT-GenRM 高 8%）。

## Method Overview（方法概述）

设 $t$ 为 RM 任务指令、$x$ 为 prompt 上下文、$(y_a, y_b)$ 为两个候选
回答。一个成对 GenRM $G_\theta$ 产生
$(R, s) \sim G_\theta(\cdot \mid t, x, y_a, y_b)$，其中 $R$ 是内部
推理轨迹，$s$ 是最终偏好标签。

考虑两类输出：
- **二元**：$s \in \{a, b\}$。
- **多类**：$s \in \{-3,-2,-1,1,2,3\}$，绝对值表示偏好强度。

### Architecture / Algorithm（架构 / 算法）

**第 1 阶段 —— 长 CoT 预热 SFT。**
对每个训练样本，用一个预训练 reasoner（$\text{QwQ-32B}$）生成 $M{=}10$
条 CoT 轨迹 $\{(\hat R_{ij}, \hat s_{ij})\}$。在与 ground-truth 标签
$s_i$ 一致的那些里，挑**最长**的一条。这就把 SFT 数据偏向了**深而带
自反思**的推理。

$$
L_{\text{SFT}}(\theta) = \mathbb{E}\big[-\log G_\theta(s \mid t,x,y_a,y_b, \hat R) \;-\; \log G_\theta(\hat R \mid t,x,y_a,y_b)\big].
$$

**第 2 阶段 —— 基于规则的 RL（仅准确率 GRPO）。**
令 $\tau = (t,x,y_a,y_b)$：

$$
L_{\text{GRPO}}(\theta) =
\mathbb{E}\!\left[\tfrac1G \sum_i \min\!\left( \rho_i A_i,\;
\text{clip}(\rho_i, 1{-}\epsilon, 1{+}\epsilon) A_i \right)\right]
- \beta D_{\text{KL}}(G_\theta \Vert G_{\theta_{\text{ref}}}),
$$

其中 $\rho_i = G_\theta(\hat R_i,\hat s_i\mid\tau) / G_{\theta_\text{old}}(\hat R_i,\hat s_i\mid\tau)$
；二元任务的奖励是：标签匹配 $1.0$，否则 $0.0$；多类任务正确为 $1.0$、
仅符号正确为 $0.5$、否则 $0.0$。RL 把 SFT 时啰嗦的风格收紧的同时
保留了效果。

**第 3 阶段 —— 成对 RLHF。**
给定 $B$ 个 prompt、每个 $G$ 个回答的一个 batch，构建斜对称矩阵
$D \in \mathbb R^{GB \times GB}$，多类任务下 $d_{ij} = -s$，二元任务
下 $d_{ij} = -\tilde s / |\hat R|$（用推理长度做置信度加权）。
GRPO advantage 变为

$$
\hat A_i \;=\; \frac{\sum_{j=1}^G d_{ij}}{\sqrt{\tfrac{G}{2(G-1)}\sum_{i,j}d_{ij}^2 + G\epsilon}},
$$

论文证明它在 $r_i - \bar r = \tfrac1G\sum_j d_{ij}$ 这一恒等式下与
$\hat A_i = (r_i - \bar r)/(\hat\sigma_r + \epsilon)$ 完全一致。
点态奖励**从未被显式构造**。

### Training Objective（训练目标）
见上 $L_{\text{SFT}}$ 与 $L_{\text{GRPO}}$。RL 由准确率驱动，并对参考
模型做 KL 正则。

## Experiments & Results（实验与结果）

**训练数据。** 6k（二元）/ 4k（多类）样本，从 HelpSteer2-Preference
过滤而来；底座 Llama-3.1-8B-Instruct。

**基准。** ID：HelpSteer2-Preference（验证集）。中等漂移：
HelpSteer3-Preference。OOD：RewardBench、RM-Bench。端到端策略：
HH-RLHF + AlpacaEval2 / GPT-4 评判。

### RM 准确率（二元，节选）

| Benchmark | Think-RM (SFT+RL) | BT RM | CoT-GenRM (GT) + 垂直×16 | Δ vs 最佳 baseline |
|-----------|-------------------|-------|--------------------------|--------------------|
| HelpSteer2 val | **81.53** | 75.57 | 80.11 | +1.4 |
| HelpSteer3 avg | **73.28** | 71.88 | 72.16 | +1.1 |
| RewardBench avg | **86.35** | 78.07 | 81.81 | +4.5 |
| RM-Bench avg | **75.06** | 68.27 | 68.11 | **+6.8** |
| RM-Bench Math | **72.25** | 59.48 | 59.15 | **+12.8** |

### 端策略性能（AlpacaEval2）

| RLHF 管线 | LC WR | WR | Avg. Len |
|-----------|-------|------|----------|
| Base（Llama-3.1-8B-Instruct）         | 24.54 | 31.11 | 2296 |
| 点态 RLHF + BT RM                     | 27.49 | 33.14 | 2300 |
| 成对 RLHF + CoT-GenRM (GT)            | 31.85 | 40.30 | 2430 |
| **成对 RLHF + Binary Think-RM**       | 31.56 | **47.20** | 2838 |
| **成对 RLHF + Multiclass Think-RM**   | **31.94** | 42.68 | 2574 |

### 消融
预热数据用"最长正确 CoT"比"最短正确 CoT"准确率更高，但代价是
推理变长。

## Strengths（优点）
- **正交的扩展轴。** 区分了"水平"（更深的单条链）vs "垂直"
  （更多链 + 投票），并在推理密集的 RM 任务上证明水平更好。
- **去掉了点态转换。** 成对 RLHF 推导干净，在自然恒等式下精确
  恢复了标准 GRPO，使得在已有 RLHF 代码库中接入近乎"即插即用"。
- **数据效率强。** 6k 样本、sub-10B 规模就在 RM-Bench 上达到 SOTA ——
  对"越大越好"的趋势是个清新反例。
- **长 CoT 的质量控制。** SFT 之后用仅准确率的基于规则 RL，在保住
  性能的同时还**把推理长度收短**，缓解了一直存在的"越长越贵越噪"
  问题。

## Limitations & Weaknesses（局限 / 不足）
- **实验止步 8B。** 所有结果用 Llama-3.1-8B-Instruct，作者自己说
  "把更大的模型整合进完整成对 RLHF 管线在算力上不可承受" —— 因此 8B
  以上的扩展行为未验证。
- **预热依赖 QwQ-32B 作为 teacher。** Think-RM 的质量被 teacher
  reasoner 上限封顶；如果 QwQ 在某领域（如非常专业的代码评审）推理
  不深，Think-RM 就继承这个局限。
- **成对评分代价。** 成对 RLHF 每个 prompt 组需要 $\mathcal{O}(G^2)$
  次 GenRM 调用，每次现在还要吐 ~1k token。每个 RL 步的算力显著高于
  点态 BT-RM RLHF —— AlpacaEval2 的收益要与之权衡。
- **只探索了两种输出形式。** 仅讨论了二元和 6 类多类；点态标量 GenRM
  没有被处理，尽管框架很可能能扩展。
- **HH-RLHF 太"易"。** 作者自己承认 HH-RLHF prompt 比较简单，所以
  CoT-GenRM 与 Think-RM 在 LC WR 上接近；更难的 RLHF 数据集会更具
  区分度。

## Open Questions（开放性问题）
- 水平扩展对**点态** GenRM（非成对任务）也有帮助吗？
- 长内部推理轨迹能否充当**过程奖励**信号（每步的信用），而不只是
  最终标签的 rationale？
- 当底座超过 8B 时，**测试时算力 vs 质量**的 Pareto 与垂直扩展相比
  会怎样？
- 水平 + 垂直**联合**（既长 CoT 又对 $k$ 条投票）—— 直觉是两者最佳
  的合成，但论文未报告。
- 向**不可验证任务**（创意写作、多准则有用性）的迁移 —— 仅准确率的
  RL 奖励隐含假设有 ground-truth 标签。

## Connections（关联）
- **Builds on：** [[generative-reward-model]]、[[reinforcement-learning-from-human-feedback]]、[[chain-of-thought]]、[[group-relative-policy-optimization]]
- **Motivated by：** [[reward-hacking]]（BT RM 已知失败模式）
- **Compared with：** Bradley–Terry 奖励建模、CoT-GenRM 加垂直推理时
  扩展（$m{=}16$ 多数投票）
- **Extended by：** *（随 wiki 增长填入）*
- **Related concepts：** [[chain-of-thought]]、[[group-relative-policy-optimization]]
- **作为先前 RLHF 基础被引用的：** InstructGPT (Ouyang 2022)、Deep RL
  from Human Preferences (Christiano 2017)、DPO (Rafailov 2023)、
  DeepSeek-R1 (Guo 2025, GRPO 来源)
- **同作者其他工作（本 wiki 内）：** *(尚无)*

## Personal Notes（个人笔记）
- 论文核心赌注是：**一条很长、带自反思的轨迹**作为奖励信号，比
  **多条短轨迹的平均**更好。直觉上这把 o1 / R1 的洞见从策略层搬到
  了奖励建模层 —— 即*评判者也应该想很久*。
- 成对 advantage 的推导是最可迁移的部分：5 行就证完了"成对 GenRM
  无损接入 GRPO"。我希望看到它被推广到 PPO/GAE 与 DPO 风格的离线
  训练。
- "最长正确"的预热挑选是一个可复用的小技巧 —— 一种隐式的"按长度做
  rejection sampling"，而不仅仅是按正确性。
- 一个开放顾虑：如果 RM-Bench 本身有长度偏置，1k token 的评判模型
  可能在偷这个漏洞。AlpacaEval2 用了长度受控版本部分排除了**策略侧**
  的这个顾虑，但在 **RM 评估侧**没有排除。

## BibTeX
```bibtex
@article{hong2025thinkrm,
  title  = {Think-RM: Enabling Long-Horizon Reasoning in Generative Reward Models},
  author = {Hong, Ilgee and Yu, Changlong and Qiu, Liang and Yan, Weixiang and Xu, Zhenghao and Jiang, Haoming and Zhang, Qingru and Lu, Qin and Liu, Xin and Zhang, Chao and Zhao, Tuo},
  journal= {arXiv preprint arXiv:2505.16265},
  year   = {2025},
  url    = {https://arxiv.org/abs/2505.16265}
}
```
