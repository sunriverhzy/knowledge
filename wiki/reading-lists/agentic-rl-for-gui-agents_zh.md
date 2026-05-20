---
title: "阅读清单：面向 GUI Agent 的 Agentic RL"
domain: [agent, rl]
difficulty: advanced
last_updated: 2026-05-20
---

# 阅读清单：面向 GUI Agent 的 Agentic RL

## 目的
面向希望理解 RL 如何训练 GUI / computer-use agent 的研究者——从 offline 初始化到 online 交互再到持续自改进。覆盖算法、数据构造、奖励设计和基础设施。

## 前置概念
- [[gui-agent]]
- [[reinforcement-learning-with-verifiable-rewards]]
- [[group-relative-policy-optimization]]
- [[multi-turn-gui-rl]]

---

## Stage 1 —— 基础：环境与 Offline RL

*理解 GUI RL 优化的对象，以及如何从静态数据中学习。*

1. [[xie-2024-osworld]] —— *真实电脑 benchmark；execution-based 评测。*
2. [[rawles-2024-androidworld]] —— *动态 mobile RL 环境，带可验证奖励。*
3. [[zhou-2023-webarena]] —— *带 executable verification 的真实 web 环境。*
4. [[bai-2024-digiirl]] —— *DigiRL：经典 offline-to-online 框架。理解训练流水线的起点。*
5. [[bai-2025-digi-q]] —— *Digi-Q：offline Q-learning；冻结 backbone + 推理时 Best-of-N。*

## Stage 2 —— 核心方法：GRPO / RLVR for GUI

*Agentic RL 的主流算法族。*

6. [[luo-2025-gui-r1]] —— *GUI-R1：RLVR + GRPO；3K 样本；涌现 System-2 推理。GUI Agentic RL 的"Hello World"。*
7. [[lu-2025-ui-r1]] —— *UI-R1：rule-based RL + DAST（难度自适应慢思考）。*
8. [[qi-2024-webrl]] —— *WebRL：self-evolving curriculum + Outcome-supervised RM。*
9. [[wei-2025-webagent-r1]] —— *M-GRPO：轨迹级多轮优化。*
10. [[xu-2025-mobilerl]] —— *MobileRL：AdaGRPO + 难度加权。*

## Stage 3 —— 规模化：Online RL、Hybrid、Hierarchical

*超越 offline：真实环境交互、hybrid 策略、层次化分解。*

11. [[lai-2025-computerrl]] —— *ComputerRL：端到端 online RL；API-GUI hybrid；Entropulse。*
12. [[wu-2025-hi-agent]] —— *Hi-Agent：层次化 Planner+Executor；87.9% AitW。*
13. [[lu-2025-ui-s1]] —— *UI-S1：semi-online RL（轨迹 patching）。*
14. [[shi-2025-mobilegui-rl]] —— *MobileGUI-RL：并行模拟器的可扩展 online GRPO。*
15. [[putta-2024-agent-q]] —— *Agent Q：MCTS 引导搜索 → DPO。"Search is data。"*

## Stage 4 —— 奖励设计与验证

*如何在稀疏奖励 GUI 任务中获得正确信号。*

16. [[cui-2026-agentic-reward-modeling]] —— *VAGEN：主动 verifier agent 探测环境获取无噪声 RLVR。*
17. [[zhang-2025-progrm]] —— *ProgRM：自动进度里程碑提取 → 稠密奖励。*
18. [[xiong-2025-gui-pra]] —— *GUI-PRA：带 UI 状态变化动态记忆的过程奖励。*
19. [[xi-2025-agentprm]] —— *AgentPRM：Promise + Progress 信号的逐步 credit。*
20. [[hu-2026-faithful-mobile-gui]] —— *Faithful-Agent：GuAE 解决稀疏 GUI RL 中的 advantage collapse。*

## Stage 5 —— World Models 与数据效率

*通过学得的环境模型突破 I/O wall。*

21. [[ding-2026-dynaweb]] —— *DynaWeb：Web World Model；dream rollout 1000× 吞吐。*
22. [[luo-2025-vimo]] —— *ViMo：视觉 GUI world model（图像级预测）。*
23. [[xu-2026-code2world]] —— *Code2World：可渲染代码 world model。*
24. [[xu-2026-mobile-world-model]] —— *Mobile World Model 如何指导 GUI Agent：4 种模态对比。*
25. [[zhang-2026-m2-miner]] —— *M2-Miner：MCTS 多 agent 数据挖掘；效率提升 64×。*

## Stage 6 —— 前沿：Continual RL 与多平台

*下一阶段：agent 随环境演化持续改进。*

26. [[liu-2026-continual-gui-agents]] —— *第一个 GUI agent 持续 RL 框架（GUI-AiF）。*
27. [[guo-2026-acurl]] —— *ACuRL：零人工数据的自主 curriculum RL。*
28. [[xu-2026-mobile-agent-v3-5]] —— *Mobile-Agent-v3.5：MRPO 多平台 RL；OSWorld 56.5%。*
29. [[yang-2026-cgl]] —— *CGL：通过 RFT 的持续 GUI 学习。*

---

## 一路上要掌握的关键概念

- [[agentic-rl]]
- [[reinforcement-learning-with-verifiable-rewards]]
- [[group-relative-policy-optimization]]
- [[multi-turn-gui-rl]]
- [[gui-io-wall]]
- [[gui-world-model]]
- [[api-gui-hybrid-action-space]]
- [[gui-grounding]]
