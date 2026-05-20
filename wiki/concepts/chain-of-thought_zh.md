---
title: "Chain of Thought"
aliases: ["CoT", "Chain-of-Thought Reasoning", "Long CoT", "思维链"]
domain: llm
tags: [chain-of-thought, prompt-engineering, reasoning, llm, test-time]
source_count: 1
last_updated: 2026-04-27
---

# 思维链（Chain of Thought）

## 定义
**思维链（CoT）** 指语言模型在给出最终答案之前，先显式地输出一段
**逐步推理过程**的模式。CoT 既可以由提示词诱发（*"我们一步一步想"*），
也可以来自针对推理痕迹的有监督微调，还可以来自"奖励末端正确"的
强化学习目标。

## 直觉
预训练教 LLM 输出*听起来合理*的下一个 token。如果没有显式的推理痕迹，
复杂问题就被压缩到一次前向传播里 —— 测试时算力太少，难以解开。
CoT 的作用是**把更多前向传播（即更多 token）分配给难题**，让中间
计算落到上下文窗口里、可以被反复 attend。

## 历史背景
- **2022** —— Wei et al., *Chain-of-thought prompting elicits reasoning
  in large language models* —— 在 few-shot 提示中加入推理范例，便能
  解锁多步数学 / 逻辑推理。
- **2022** —— Wang et al., *Self-Consistency* —— 采样多条 CoT，对最终
  答案做多数投票；"垂直"测试时扩展的模板。
- **2023** —— Yao et al., *Tree-of-Thoughts* —— 在推理树上做结构化
  搜索。
- **2024** —— OpenAI o1 / o3：通过 RL 训练的**长时程 CoT**，把单条
  推理轨迹从 ~100 token 推到几千 token。
- **2025** —— DeepSeek-R1（Guo et al.）公开演示：长 CoT 能力可以通过
  基于规则的 RL + GRPO 系统性地训练得到；QwQ-32B、Grok-3 及后续模型
  纷纷跟进。长 CoT 成为前沿模型的默认配置。

## 关键变体 / 子类型

- **短 CoT（提示驱动）** —— 上下文中放几条范例；推理痕迹大约 100–500
  token。经典的 Wei et al. 范式。
- **Self-Consistency / 垂直扩展** —— 采样 $m$ 条 CoT，聚合答案。提升
  可靠性，但会被"浅层推理"上限卡住。
- **结构化 CoT（Tree-of-Thoughts、Graph-of-Thoughts）** —— 在中间状态
  上分支 / 搜索。
- **长时程 / 长 CoT** —— 单条轨迹 1k–10k+ token，经常带自反思、回溯、
  反事实推理。通过"长 CoT 上 SFT + RL"诱发（DeepSeek-R1, o1）。
  [[hong-2025-think-rm]] 把这种风格用到了*奖励建模*上而非通常的
  任务策略。
- **CoT 作为奖励 / verifier 信号** —— 用推理痕迹要么给出最终判决
  （[[generative-reward-model]]），要么给出过程奖励（过程奖励模型）。

## 技术细节

### 水平 vs 垂直推理时扩展
"在推理时多花算力"的两个正交轴：

- **垂直扩展**：独立采样 $m$ 条短 CoT，多数投票或平均聚合。
  总 token 数：$m \times L_\text{short}$。
- **水平扩展**：在一条轨迹内长度为 $L_\text{long}$，包含自反思和
  内部分支。总 token 数：$L_\text{long}$。

经验上（如 [[hong-2025-think-rm]] 所示），水平扩展在**需要深而连贯
推理**的任务（代码、数学、长多轮对话）上占优；当错误是可被多数投票
过滤的无偏噪声时，垂直扩展占优。

### 训练长 CoT
两个常见配方：
1. **SFT 预热** —— 在长 CoT 数据上做 SFT（用 o1 / QwQ 之类前沿
   reasoner 合成、按"最长且正确"挑选），然后用**带可验证奖励的基于
   规则的 RL**（DeepSeek-R1；[[hong-2025-think-rm]] 把这套配方用到了
   *奖励建模*上而不是下游任务）。
2. **从底座 LLM 直接 RL** —— 可行但比 SFT+RL 配方数据效率差。

## 关键论文

| 论文 | 年份 | 贡献 |
|------|------|------|
| [[hong-2025-think-rm]] | 2025 | 把长时程 CoT（通过 SFT+RL）应用于*奖励建模*，让裁判可以做自反思判断。 |

*（待摄取的奠基论文：Wei 2022 — CoT prompting；Wang 2022 —
Self-Consistency；Yao 2023 — Tree of Thoughts；Guo 2025 — DeepSeek-R1。）*

## 与相关概念的对比

|                     | 短 CoT（提示驱动）  | 长 CoT（RL 训练）        | Tree-of-Thoughts        |
|---------------------|--------------------|--------------------------|-------------------------|
| 每个答案的轨迹数    | 1（或小 $m$）      | 1                        | 多（树节点）            |
| 每条轨迹的 token 数 | 100–500            | 1k–10k+                  | 节点小                  |
| 是否需要训练？      | 否                 | 是（SFT + RL）           | 通常仅推理时             |
| 自反思？            | 罕见               | 常见                     | 由外部搜索控制          |
| 最适用于            | 简单多步           | 复杂、微妙的推理         | 组合搜索                |

## 当前研究前沿
- **水平 vs 垂直的权衡曲线** —— 模型规模变大时 Pareto 前沿是什么形状？
- **CoT 评测中的长度偏置** —— 长链与正确性正相关，但也抬高成本；
  如何在评测中联合度量效率。
- **CoT 给评判者用，而不只给求解者用** —— [[hong-2025-think-rm]]
  证明 CoT 对评估也有益，不止生成。
- **CoT 的忠实性** —— 推理痕迹反映的是模型真实计算，还是事后合理化？

## 参见
- [[generative-reward-model]]
- [[reinforcement-learning-from-human-feedback]]
- [[group-relative-policy-optimization]]
