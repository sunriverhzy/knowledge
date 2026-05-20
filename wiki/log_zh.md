# AI 研究 Wiki —— 操作日志

> 仅追加记录：何时发生了什么。
> 格式：`## [YYYY-MM-DD] 操作 | 描述`

---

## [2026-04-27] init | Wiki 骨架创建
- 创建 CLAUDE.md（Schema）、README.md、目录结构
- 确立三层架构、页面模板、标签登记处
- 所有文件同时创建双语（_zh）版本

## [2026-04-27] ingest | Think-RM：让生成式奖励模型具备长时程推理能力
- 新建：wiki/papers/reward-modeling/hong-2025-think-rm.md
- 新建概念：generative-reward-model、reinforcement-learning-from-human-feedback、reward-hacking、chain-of-thought、group-relative-policy-optimization
- 新建会议页：arxiv.md
- 更新：index.md、overview.md

## [2026-04-27] ingest | 怎样的奖励模型才是好"老师"？—— 一个优化视角
- 新建：wiki/papers/reward-modeling/razin-2025-good-teacher.md
- 新建概念：reward-variance、reward-model-as-teacher
- 新建会议页：neurips.md
- 新建对比页：think-rm-vs-good-teacher.md
- 更新：3 个既有概念页、index.md、overview.md

## [2026-04-27] maintenance | 双语镜像补全
- 为 12 个缺少中文版的 wiki 页面创建 _zh 版本
- 更新 CLAUDE.md Step 10 正式化双语要求
- 更新 README.md 双语文档部分

## [2026-05-19] ingest | GUI Agents with RL 综述 (arXiv:2604.27955)
- 下载综述 PDF + 55 篇相关 GUI/computer-use SOTA 论文到 raw/
- 新建：wiki/reading-lists/gui-agent-computer-use-sota.md（调研笔记）

## [2026-05-20] ingest | 批量摄取：54 篇 GUI Agent / Computer Use 论文
- 新建 8 个概念：gui-agent、computer-use-agent、gui-grounding、reinforcement-learning-with-verifiable-rewards、api-gui-hybrid-action-space、gui-io-wall、multi-turn-gui-rl、gui-world-model
- 新建 54 个论文页于 wiki/papers/agent/
- 新建 7 个会议页（iclr、acl、eccv、emnlp、cvpr、aaai、ijcai）
- 更新：index.md、overview.md（主线 2 扩展）

## [2026-05-20] ingest | 37 篇最新 GUI/CUA/mobile-use 论文（arXiv + ICLR 2026 + ACL 2026）
- 下载 37 篇新 PDF 到 raw/（raw 总 PDF 数：149）
- 新建 37 个论文页于 wiki/papers/agent/
- 更新：index.md（共 93 篇论文）、overview.md

## [2026-05-20] query | "哪些论文涉及 Agentic RL？这是否可以是一个新的概念簇？"
- 识别出 24+ 篇论文构成 Agentic RL 簇
- 产出深度分析：训练流水线、数据构造、RL 算法、奖励三层塔、基础设施
- 结果：回写为 wiki/concepts/agentic-rl.md + wiki/reading-lists/agentic-rl-for-gui-agents.md
- 新建：wiki/log.md（本文件）
- 更新：CLAUDE.md（Query → Wiki 回写规则）

## [2026-05-20] query | "用 RL 训练的 verifier agent 为其他 agent 产出奖励——前沿与落地研究有哪些？"
- 交叉引用 7+ 个 wiki 页面 + 3 篇 wiki 外新论文（GUI-Shepherd、ADMIRE、Agent-RewardBench）
- 结果：回写为 wiki/comparisons/verifier-agent-reward-systems.md
- 更新：wiki/index.md、wiki/index_zh.md
