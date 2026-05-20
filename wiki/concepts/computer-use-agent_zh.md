---
title: "Computer-Use Agent"
aliases: ["CUA", "Computer Use Agent", "Computer-using Agent", "计算机使用智能体"]
domain: agent
tags: [agent, computer-use, gui-agent, rl, multimodal]
source_count: 8
last_updated: 2026-05-20
---

# Computer-Use Agent（计算机使用智能体）

## 定义
**Computer-Use Agent（CUA）** 是一种能够操作真实电脑——包括操作系统、安装的应用程序、浏览器和文件系统——来完成任意用户指定任务的 AI 系统。与针对单一平台（移动端或 Web）的狭义 GUI agent 不同，CUA 需要处理**跨平台、跨应用**的 workflow：浏览网页、读写文件、编写代码、填写表单、发送邮件等，在单次任务执行中完成。

其定义性特征是**通用性**：CUA 必须能操作任何人类能操作的软件，包括没有外部 API 的私有应用。

## 直觉
CUA 是"数字员工"愿景的 AI 实现：给它一个任务如"研究竞争对手并在 Google Docs 中准备一份报告"，它会自己打开浏览器、搜索、记笔记、切换到 Docs、写报告——全程自主。这超越了单应用自动化脚本，因为 agent 必须跨应用规划、管理跨上下文窗口的状态，并在真实系统上从错误中恢复。

## 历史背景
"computer use"这一术语由 Anthropic 的 **Claude Computer Use**（2024 年底）和 OpenAI 的 **Computer-Using Agent**（2025 年 1 月）普及，这些商业发布验证了范式并加速了开放研究。

开源 CUA 系统随即涌现：**Agent S** / **Agent S2**、**OpenCUA**、**OS-Copilot**、**ComputerRL**、**UltraCUA**、**UFO2**（Desktop AgentOS）。

## 关键变体 / 子类型

- **纯视觉 CUA**：仅依赖截图，不使用 accessibility API。跨平台覆盖最广。例：Claude Computer Use, UI-TARS。
- **Hybrid-action CUA**：结合截图级 GUI 控制与 API / tool 调用。例：ComputerRL, UltraCUA, UFO2。
- **Agentic framework CUA**：在 VLM 外层包装记忆、规划器、工具接口（搜索、代码执行）。例：Agent S, Agent S2, OS-Copilot。
- **RL 训练 CUA**：使用 RLVR / GRPO 进行在线 / 半在线改进。例：UI-TARS, ComputerRL, OpenCUA。

## 技术细节

### CUA 流水线

```
用户指令
    │
    ▼
观察：截图 [+ accessibility tree] [+ 历史摘要]
    │
    ▼
VLM backbone：规划 → 推理 → 选择动作
    │
    ▼
动作：click(x,y) / type(text) / scroll / key / call_api(fn, args)
    │
    ▼
环境：OS / 浏览器 / 应用响应
    │
    ▼
Verifier：规则检查 / 状态 diff / LLM judge → 奖励
    │
    ▼
（循环直到任务完成或步骤预算耗尽）
```

### API-GUI hybrid action space
真实 computer use 得益于两个动作层：

| 层次 | 示例 | 适用时机 |
|------|------|----------|
| **GUI（低层）** | `click(450, 320)` | 无 API 可用；需要视觉确认 |
| **API / Tool（高层）** | `read_file(path)`, `search_web(query)` | API 存在；确定性；轨迹更短 |

[[lai-2025-computerrl]] 与 [[yang-2025-ultracua]] 证明 hybrid routing 能显著缩短轨迹长度。见 [[api-gui-hybrid-action-space]]。

### 评测
CUA 通常在**基于执行的** benchmark 上评测，通过检查真实副作用验证任务完成：
- **OSWorld**（`2404.07972`）：真实 Linux VM 中的 369 个任务。
- **Windows Agent Arena**（`2409.08264`）：以 Windows 为重点的多模态任务。
- **ComputerRL**（`2508.14040`）：带 execution-based 奖励的端到端在线 RL。

## 关键论文

| 论文 | 年份 | 贡献 |
|------|------|------|
| [[xie-2024-osworld]] | 2024 | 核心 desktop CUA benchmark；369 个任务；execution-based 评测；第一个真实 computer RL 环境。 |
| [[wang-2025-opencua]] | 2025 | CUA 的开放数据、模型和训练基础设施。 |
| [[lai-2025-computerrl]] | 2025 | 端到端 online RL；API-GUI hybrid action space；熵坍缩缓解。 |
| [[agashe-2025-agent-s2]] | 2025 | 组合式 generalist-specialist CUA；OSWorld SOTA。 |
| [[yang-2025-ultracua]] | 2025 | 带 hybrid action 的 Foundation CUA；视觉 primitive + 程序化 tool 路由。 |

## 与相关概念的对比

|                   | [[gui-agent]]           | Computer-Use Agent          | API Agent                 |
|-------------------|-------------------------|-----------------------------|---------------------------|
| 平台范围          | 单一（web/mobile/OS）   | 跨平台                      | 仅 API 可访问的服务       |
| 动作模态          | GUI 像素级              | GUI + API / tools           | 仅 API 调用               |
| 记忆 / 状态       | 单次 episode 上下文     | 跨应用的持久记忆             | 无状态调用                |
| 典型评测          | OSWorld, WebArena       | OSWorld, Windows Arena      | SWE-bench, code exec      |

## 当前研究前沿
- 无平台特定微调的**跨平台泛化**。
- 跨长任务和会话的**持久记忆**。
- **权限与安全层**：CUA 能在不经用户逐步确认的情况下做什么？
- **World-model-based 规划**：见 [[gui-world-model]]。
- **I/O 高效训练**：见 [[gui-io-wall]]。

## 参见
- [[gui-agent]]
- [[gui-grounding]]
- [[api-gui-hybrid-action-space]]
- [[reinforcement-learning-with-verifiable-rewards]]
- [[gui-io-wall]]
- [[multi-turn-gui-rl]]
