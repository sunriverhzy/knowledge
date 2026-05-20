---
title: "GUI Agent"
aliases: ["GUI Agents", "图形用户界面 Agent", "GUI 智能体", "Screen Agent"]
domain: agent
tags: [agent, gui-agent, multi-agent, rl, multimodal, computer-use]
source_count: 20
last_updated: 2026-05-20
---

# GUI Agent（GUI 智能体）

## 定义
**GUI Agent** 是一种通过**视觉观察图形用户界面（GUI）**——主要是截图——并通过生成**类人交互**（鼠标点击、键盘输入、滚动、拖拽）来完成任务的 AI 系统。与 CLI-based agent 或 API-based agent 不同，GUI agent 在像素 / accessibility-tree 层面操作，能够自动化任何人类可操作的软件，不论其是否有对外的程序化 API。

形式上，GUI agent 可被建模为在**部分可观测马尔可夫决策过程（POMDP）**中行动的策略 $\pi_\theta$：

$$
(S, A, T, R, \gamma), \quad o_t = \text{screenshot}(s_t) \in \mathbb{R}^{H \times W \times 3}
$$

agent 观察截图 $o_t$，选择动作 $a_t$（如 `click(x, y)`, `type("text")`, `scroll(delta)`），获得由任务完成情况或中间验证信号派生的奖励 $r_t$。

## 直觉
GUI agent 本质上是一个"能看见屏幕并像人一样使用电脑"的机器人——通常无法访问源代码、API 或 DOM 树。核心挑战是把**视觉感知**（屏幕上有什么？）→ **动作规划**（下一步该做什么？）→ **grounding**（精确点哪里？）三个子问题串起来——而且要在稀疏、延迟的奖励下完成。

## 历史背景

| 时期 | 里程碑 |
|------|--------|
| 2017 | **MiniWob / MiniWob++** —— 第一批面向 web GUI RL 的合成 HTML5 benchmark。 |
| 2021–2022 | **WebGPT**, **WebShop** —— 将 LLM 应用于 web 浏览和购物任务。 |
| 2023 | **Mind2Web**, **WebArena** —— 真实 web-agent benchmark；**Android in the Wild** —— 大规模手机数据。 |
| 2024 | **OSWorld**, **AndroidWorld** —— 真实电脑 / 手机环境；**UI-TARS**, **OS-ATLAS** —— 带 RL 训练的原生 VLM GUI agent；商业：**Claude Computer Use**, **OpenAI CUA**。 |
| 2025–2026 | **GRPO / RLVR** 成为主流 GUI RL 算法；**ComputerRL**, **DynaWeb**, **UI-TARS-2** —— 扩展 RL 用于 computer use；多轮与层次化 agent。 |

## 关键变体 / 子类型

- **Web Agent** —— 在浏览器中操作网站。环境：[[zhou-2023-webarena]], [[koh-2024-visualwebarena]], [[chezelles-2024-browsergym]]。
- **Desktop / OS Agent** —— 在完整操作系统中操作，含文件 I/O、多应用 workflow。环境：[[xie-2024-osworld]], [[bonatti-2024-windows-agent-arena]]。
- **Mobile Agent** —— 在智能手机上通过手势触摸操作。环境：[[rawles-2024-androidworld]], [[rawles-2023-android-in-the-wild]]。
- **Computer-Use Agent** —— 更宽泛的术语，覆盖跨平台 agent，同时结合 web、desktop 乃至 mobile；见 [[computer-use-agent]]。

## 技术细节

### 观察空间
- **截图**（主要）：$H \times W \times 3$ 像素阵列。
- **Accessibility tree**（可选）：结构化 DOM 或 OS accessibility 元数据，含元素名称、bounding box、角色。
- **历史**：之前的截图、动作，以及可选的 CoT 摘要。

### 动作空间
| 类型 | 示例 |
|------|------|
| 点击 | `click(x, y, button)` |
| 输入 | `type("search query")` |
| 滚动 | `scroll(x, y, delta)` |
| 按键 | `key("Enter")` |
| API（hybrid） | `get_file_content(path)` |

### 训练范式
见 [[reinforcement-learning-with-verifiable-rewards]]、[[group-relative-policy-optimization]]。

- **Offline RFT**（常见入口）：在人类 demonstration 上 SFT + 对轨迹偏好对做 DPO / GRPO。
- **Online RL**：agent 与真实或模拟环境交互；冷启动需要 curriculum learning。
- **Hybrid**：offline 预热 → 半在线 / 在线微调。

### 奖励工程
三层 pyramid：

1. **规则奖励**：坐标在 bounding box 内、URL 匹配、表单提交、DOM / 文件状态检查。
2. **LLM-as-Judge**：模型从截图评估任务完成情况。
3. **Learned RM**：outcome-supervised reward model（ORM）或 process reward model（PRM）。

## 关键论文

| 论文 | 年份 | 贡献 |
|------|------|------|
| [[xie-2024-osworld]] | 2024 | 第一个带 execution-based evaluation 的真实 OS benchmark。 |
| [[qin-2025-ui-tars]] | 2025 | 原生截图-only GUI agent；跨平台 SFT+RL；在 10+ benchmark 上 SOTA。 |
| [[bai-2024-digiirl]] | 2024 | device-control agent 的 offline-to-online RL；VLM-as-evaluator。 |
| [[luo-2025-gui-r1]] | 2025 | 规则 RLVR + GRPO；3K 样本 → 涌现推理能力。 |
| [[lai-2025-computerrl]] | 2025 | API-GUI hybrid action space；面向 computer use 的端到端 online RL。 |

## 与相关概念的对比

|                       | GUI Agent               | [[computer-use-agent]] | CLI Agent               |
|-----------------------|-------------------------|------------------------|-------------------------|
| 输入模态              | 截图（+ 可选 AT）       | 截图 + APIs            | 文本 / 命令输出          |
| 输出动作空间          | 像素级鼠标 / 键盘       | GUI + API / tools      | Shell 命令               |
| 覆盖范围              | 任意软件                | 任意软件 + APIs        | 仅有 API 的软件          |
| RL 难度               | 高（视觉、稀疏）        | 中（hybrid 信号）      | 低（确定性）             |
| Benchmark 示例        | OSWorld, AndroidWorld   | ComputerRL, OpenCUA    | SWE-bench, InterCode    |

## 当前研究前沿
- **Hybrid action space**：结合 GUI 像素级动作与高层 API / tool 调用。
- **[[gui-grounding]]**：作为独立 RL 问题的精准 grounding。
- **[[multi-turn-gui-rl]]**：多轮 GRPO 与长时程 credit assignment。
- **[[gui-world-model]]**：model-based RL 突破 [[gui-io-wall]]。
- **层次化控制**：在快速 System-1 grounding 与慢速 System-2 推理之间路由。
- **安全与权限模型**：如何训练不会意外删除文件或支付款项的 agent。

## 参见
- [[computer-use-agent]]
- [[gui-grounding]]
- [[reinforcement-learning-with-verifiable-rewards]]
- [[multi-turn-gui-rl]]
- [[gui-io-wall]]
- [[api-gui-hybrid-action-space]]
- [[group-relative-policy-optimization]]
