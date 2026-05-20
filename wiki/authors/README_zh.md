# `wiki/authors/` —— 作者页

每位重要研究者对应一个 Markdown 页面。

## 何时新建作者页

只要满足**任一**条件就新建作者页：

- 该作者在本 Wiki 中已有 **≥ 2 篇论文**。
- 该作者是领域内的知名人物，并且你在主动跟踪其工作，即使目前只摄取了
  一篇其论文。
- 你在某个论文页中很想加一个"同作者其他工作"的交叉引用。

## 页面格式

使用 `CLAUDE.md` 中的**模板 C**。必需小节：

1. YAML frontmatter（title、affiliation、domain、homepage）。
2. `Research Focus`。
3. `Papers in This Wiki` —— 用 `[[paper-slug]]` 链接的列表。
4. `Notable Contributions to the Field`。
5. `Key Collaborators (in this wiki)`。
6. `Research Group / Lab`。

## Slug 约定

`{firstname-lastname}`（小写、kebab-case、仅 ASCII）。

示例：
- `ilya-sutskever`
- `andrej-karpathy`
- `chelsea-finn`

对非 ASCII 姓名，使用罗马化形式，并在页面正文中给出原始姓名。

## 不要做

- 不要仅因为摄取了一篇论文就立刻建作者页 —— 要等到满足上面的阈值规则。
- 不要把本属于论文页的信息搬到作者页上。
  作者页是**导航层**，不是内容档案库。
