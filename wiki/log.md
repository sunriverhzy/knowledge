# AI Research Wiki — Operations Log

> Append-only record of what happened and when.
> Format: `## [YYYY-MM-DD] operation | description`

---

## [2026-04-27] init | Wiki skeleton created
- Created CLAUDE.md (Schema), README.md, directory structure
- Established three-layer architecture, page templates, tag registry
- All files created with bilingual (_zh) counterparts

## [2026-04-27] ingest | Think-RM: Enabling Long-Horizon Reasoning in Generative Reward Models
- Created: wiki/papers/reward-modeling/hong-2025-think-rm.md
- Created concepts: generative-reward-model, reinforcement-learning-from-human-feedback, reward-hacking, chain-of-thought, group-relative-policy-optimization
- Created venue: arxiv.md
- Updated: index.md, overview.md

## [2026-04-27] ingest | What Makes a Reward Model a Good Teacher? An Optimization Perspective
- Created: wiki/papers/reward-modeling/razin-2025-good-teacher.md
- Created concepts: reward-variance, reward-model-as-teacher
- Created venue: neurips.md
- Created comparison: think-rm-vs-good-teacher.md
- Updated: 3 existing concept pages, index.md, overview.md

## [2026-04-27] maintenance | Bilingual mirroring completed
- Created _zh versions for all 12 wiki pages that lacked Chinese counterparts
- Updated CLAUDE.md Step 10 to formalize bilingual requirement
- Updated README.md bilingual documentation section

## [2026-05-19] ingest | GUI Agents with RL survey (arXiv:2604.27955)
- Downloaded survey PDF + 55 related GUI/computer-use SOTA papers to raw/
- Created: wiki/reading-lists/gui-agent-computer-use-sota.md (survey note)

## [2026-05-20] ingest | Batch ingest: 54 GUI Agent / Computer Use papers
- Created 8 new concepts: gui-agent, computer-use-agent, gui-grounding, reinforcement-learning-with-verifiable-rewards, api-gui-hybrid-action-space, gui-io-wall, multi-turn-gui-rl, gui-world-model
- Created 54 paper pages in wiki/papers/agent/
- Created 7 venue pages (iclr, acl, eccv, emnlp, cvpr, aaai, ijcai)
- Updated: index.md, overview.md (Thread 2 expanded)

## [2026-05-20] ingest | 37 latest GUI/CUA/mobile-use papers (arXiv + ICLR 2026 + ACL 2026)
- Downloaded 37 new PDFs to raw/ (total raw PDFs: 149)
- Created 37 paper pages in wiki/papers/agent/
- Updated: index.md (93 papers total), overview.md

## [2026-05-20] query | "Which papers involve Agentic RL? Is this a new concept cluster?"
- Identified 24+ papers forming Agentic RL cluster
- Produced deep analysis: training pipeline, data construction, RL algorithms, reward tiers, infrastructure
- Result: filed back as wiki/concepts/agentic-rl.md + wiki/reading-lists/agentic-rl-for-gui-agents.md
- Created: wiki/log.md (this file)
- Updated: CLAUDE.md (Query → Wiki write-back rules)

## [2026-05-20] query | "Verifier agent producing rewards for other agents — frontier and deployed research?"
- Cross-referenced 7+ wiki pages + 3 new external papers (GUI-Shepherd, ADMIRE, Agent-RewardBench)
- Result: filed back as wiki/comparisons/verifier-agent-reward-systems.md
- Updated: wiki/index.md, wiki/index_zh.md
