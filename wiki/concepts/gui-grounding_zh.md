---
title: "GUI Grounding"
aliases: ["Visual Grounding for GUI", "UI Grounding", "GUI 元素定位", "GUI 坐标定位"]
domain: agent
tags: [gui-agent, grounding, multimodal, rl, visual-grounding]
source_count: 10
last_updated: 2026-05-20
---

# GUI Grounding（GUI 坐标定位）

## 定义
**GUI Grounding** 是把**自然语言指令或高层意图**映射到图形界面上**精确屏幕坐标或 UI 元素**的任务。它是 GUI 自动化的"最后一公里"：即便 agent 规划了正确的动作序列，如果不能准确定位 `click(x, y)` 的落点，整个任务也会失败。

形式上，给定截图 $I \in \mathbb{R}^{H \times W \times 3}$ 和查询 $q$（如 "the submit button"），grounding 产生坐标：

$$
(x^*, y^*) = f_\theta(I, q), \quad \text{成功} \iff (x^*, y^*) \in \text{BBox}(e)
$$

其中 $e$ 是目标 UI 元素，$\text{BBox}(e)$ 是其真值 bounding box。

## 直觉
Grounding 是**精度任务**，不是推理任务。一个高层规划很好的模型，如果点在一个小按钮外 3 个像素，照样会失败。这促使研究者开发专门的 grounding 模型和稠密奖励函数（IoU、基于距离），与任务级稀疏奖励分开处理。

来自 [[liu-2026-infigui-g1]] 的重要发现：**为 grounding 加入 Chain-of-Thought 推理反而*降低*精度**，因为它在预测坐标时引入 hallucination。Grounding 是"System 1"任务；规划是"System 2"任务。

## 历史背景
- **2018** — *Mapping NL to Web Elements*（Pasupat et al.）：文本查询到 web 元素的早期 grounding 工作。
- **2024** — **SeeClick** 与 **ScreenSpot** 把基于 IoU 的 grounding 确立为独立 benchmark。**OS-ATLAS** 扩展到 1300 万 grounding 样本。
- **2025** — **ScreenSpot-Pro** 针对高分辨率专业屏；grounding 成为生产级 agent 的关键瓶颈。**GUI-G2** 引入 Gaussian reward 解决"1-pixel deviation = failure"。**UI-AGILE**、**SE-GUI**、**InfiGUI-G1**、**GUI-Eyes** 推进基于 RL 的 grounding。

## 关键变体 / 子类型

- **坐标回归**：直接预测 `(x, y)` 或归一化坐标。UI-TARS、OS-ATLAS、SeeClick 采用。
- **元素引用 grounding**：找到 accessibility-tree 节点，再用其 bounding box 中心。
- **Fast-thinking（System 1）grounding**：抑制推理，直接回归坐标。[[liu-2026-infigui-g1]]。
- **Active perception grounding**：先调用视觉缩放 / 裁剪工具再预测坐标。[[chen-2026-gui-eyes]]。
- **RL reward-shaped grounding**：使用连续 IoU 或 Gaussian 奖励。[[tang-2025-gui-g2]]，[[lian-2025-ui-agile]]。
- **Self-evolutionary grounding**：用 attention map 作为伪标签。[[yuan-2025-se-gui]]。

## 技术细节

### Grounding benchmarks

| Benchmark | 平台 | 指标 | 关键挑战 |
|-----------|------|------|----------|
| ScreenSpot | 跨平台 | Accuracy (@IoU 0.5) | 通用元素定位 |
| ScreenSpot-Pro | Desktop（专业） | Accuracy (@IoU 0.5) | 高分辨率小元素 |
| Mind2Web-grounding | Web | Accuracy | Web 特有元素类型 |
| AndroidWorld-grounding | Mobile | Accuracy | 移动手势区域 |

### RL grounding 奖励函数

- **二值 hit/miss**：$(x^*, y^*) \in \text{BBox}(e)$ 时 $r = 1$，否则 $0$。简单但稀疏。
- **基于距离**（UI-AGILE）：$r = \max\left(0, 1 - \frac{\text{dist}((x^*, y^*), \text{center}(e))}{\text{threshold}}\right)$。稠密梯度。
- **基于 IoU**：$r = \text{IoU}(\hat{B}, \text{BBox}(e))$，用于 bounding-box 预测。
- **Gaussian**（GUI-G2）：$r = \exp\left(-\frac{\|(x^*, y^*) - \mu_e\|^2}{2\sigma^2}\right)$。解决"1-pixel failure"问题。

## 关键论文

| 论文 | 年份 | 贡献 |
|------|------|------|
| [[li-2024-seeclick]] | 2024 | SeeClick：把 GUI grounding 确立为 VLM 的核心能力；ScreenSpot benchmark。 |
| [[you-2024-ferret-ui]] | 2024 | Ferret-UI：移动端 grounding 的任意分辨率适配；Visual CoT。 |
| [[wu-2024-os-atlas]] | 2024 | OS-ATLAS：1300 万 grounding 样本；强 baseline。 |
| [[liu-2026-infigui-g1]] | 2026 | AEPO；CoT 降低精度；fast-thinking 模板。 |
| [[tang-2025-gui-g2]] | 2025 | Gaussian 奖励建模；解决"1-pixel = failure"。 |
| [[lian-2025-ui-agile]] | 2025 | 稠密 IoU 奖励 + 裁剪重采样；ScreenSpot 提升 23%。 |
| [[chen-2026-gui-eyes]] | 2026 | Active perception：grounding 前主动调用缩放工具。 |
| [[yuan-2025-se-gui]] | 2025 | Self-evolutionary RL：attention-map 伪标签。 |

## 与相关概念的对比

|                    | GUI Grounding                         | GUI Planning                           |
|--------------------|---------------------------------------|----------------------------------------|
| 任务类型           | System 1（快速、精确）                | System 2（慢速、慎思）                  |
| 输出               | `(x, y)` 坐标                        | 动作序列 / 子目标规划                   |
| CoT 有帮助吗？     | 通常**无**（hallucination 风险）      | 通常**有**                              |
| 奖励信号           | 稠密（IoU / 距离）                    | 稀疏（任务完成）                        |
| RL 算法            | GRPO / RLVR / 连续奖励 RL             | GRPO / hierarchical RL                 |

## 当前研究前沿
- **高分辨率生产屏**：ScreenSpot-Pro 表明当前 VLM 在 4K / Retina 屏幕上仍有差距。
- **Active perception**：主动向环境请求 zoom patch，而不是硬扛高像素分辨率。
- **动态元素 grounding**：步骤间出现、消失或移动的元素（加载转圈、下拉菜单）。
- **无坐标 grounding**：用语义元素引用替代像素坐标。见 GUI-Actor。

## 参见
- [[gui-agent]]
- [[computer-use-agent]]
- [[reinforcement-learning-with-verifiable-rewards]]
- [[multi-turn-gui-rl]]
- [[api-gui-hybrid-action-space]]
