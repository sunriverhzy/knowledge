---
title: "API-GUI Hybrid Action Space"
aliases: ["Hybrid Action Space", "GUI-API Hybrid", "混合动作空间"]
domain: agent
tags: [agent, gui-agent, computer-use, api, action-space]
source_count: 4
last_updated: 2026-05-20
---

# API-GUI 混合动作空间（API-GUI Hybrid Action Space）

## 定义
**API-GUI 混合动作空间**是一种 agent 动作表示，结合了两个层次：

- **GUI 动作（低层）**：像素坐标点击、输入、滚动、按键 —— 通用但较慢。
- **API / Tool 动作（高层）**：函数调用，如 `read_file(path)`, `search_web(query)`, `checkout()` —— 快速、确定性，但只有在 API 存在时才可用。

agent **动态路由**在两个层次之间，根据是否有可用 API 以及精度还是速度更重要来决定。

## 直觉
人类不会在能直接输入路径时还费力地点开文件浏览器。类似地，一个能在文件访问任务上路由到 `get_file_content(path)` 的 agent，可以把轨迹长度缩短 10–100 倍，同时保留对任意应用进行 pure GUI 操作的能力。

## 历史背景
- **2024** —— **UFO** 和 **OS-Copilot** 首先将 tool 调用与 GUI 操作混合。
- **2025** —— **ComputerRL**（`2508.14040`）引入正式的 API-GUI hybrid action paradigm；**UltraCUA**（`2510.17790`）统一视觉 primitive 与程序化 API。
- **2025–2026** —— **MCPWorld** 和 **OSWorld-MCP** 对 hybrid computer use 做了标准化 benchmark。

## 关键变体 / 子类型

| 变体 | API 层 | GUI 层 | 路由方式 |
|------|--------|--------|----------|
| **Tool-augmented** | 函数调用 | 截图 | 显式（LLM 选择） |
| **OS-API hybrid** | Win32 / X11 / Cocoa | 像素坐标 | 学得的路由器 |
| **Browser API + DOM** | CDP / JS evaluate | 点击 / 滚动 | 混合 |
| **MCP-based** | MCP tool server | GUI fallback | MCP 协议 |

## 技术细节

**轨迹长度缩短**：类似"打开文件 X"的任务需要约 15 个 GUI 步骤（导航、点击、双击……）vs 1 个 API 调用。混合路由减少了平均轨迹长度，提升 RL 的样本效率。

**熵坍缩缓解**（ComputerRL）：纯 GUI 长轨迹倾向于收敛到退化的动作序列。用 SFT "Entropulse"和 RL 交替训练可恢复策略熵，防止坍缩。

**动作抽象**：统一接口把平台特定调用（Win32、X11、Cocoa）隐藏在标准动词之后，使训练能跨异构 OS 环境进行。

## 关键论文

| 论文 | 年份 | 贡献 |
|------|------|------|
| [[lai-2025-computerrl]] | 2025 | 正式的 API-GUI hybrid action paradigm；Entropulse；端到端 CUA 训练。 |
| [[yang-2025-ultracua]] | 2025 | 带 hybrid 视觉 primitive + API tools 的 Foundation CUA。 |
| [[zhang-2025-ufo2]] | 2025 | Desktop AgentOS：显式应用-API 路由 + GUI fallback。 |

## 参见
- [[computer-use-agent]]
- [[gui-agent]]
- [[gui-io-wall]]
- [[reinforcement-learning-with-verifiable-rewards]]
