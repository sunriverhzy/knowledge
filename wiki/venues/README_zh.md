# `wiki/venues/` —— 会议/期刊页

每个会议、期刊、workshop 对应一个 Markdown 页面。

## 用途

会议/期刊页让你能够：

- 按年份查看 Wiki 中来自某个会议的所有论文。
- 观察某个会议录用趋势。
- 通过发表来源快速定位 Wiki 内容。

## 页面格式

使用 `CLAUDE.md` 中的**模板 E**。必需小节：

1. YAML frontmatter（title、abbreviation、type、domain）。
2. `Overview` —— 简要说明该会议的范围与地位。
3. `Papers in This Wiki (by year)` —— 按年份分组的列表。
4. `Notable Trends` —— 从 Wiki 中该会议论文观察到的主题趋势。

## Slug 约定

使用**小写缩写**作为 slug。

示例：
- `neurips`
- `icml`
- `iclr`
- `cvpr`
- `arxiv`（未发表的 arXiv 预印本统一使用这个）

## 何时创建

**首次**从某个会议摄取论文时创建其页面。之后每次从同一会议摄取
都更新现有页面即可。
