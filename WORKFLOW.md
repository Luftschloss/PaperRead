# PaperRead 论文阅读工作流

已安装 7 个 skill 到 `.agents/skills/`（项目级，pi 启动时自动发现）。论文 PDF 建议放进 `Papers/`（已有 `Papers/RAG/`）。

所有 skill 都可用 `/skill:<name>` 强制调用，也可在对话中自然触发（描述匹配即加载）。

## 场景 A：只有 20 分钟 —— 快速初筛

```text
Papers/xxx.pdf
   │
   ▼
/skill:paper-analyst quick 模式
   └── 产出：三个核心贡献 + 摘要式理解
   │
   ▼ （担心作者把话说大了）
/skill:academic-paper-review
   └── 产出：Claim—Evidence 对照表（每条声明 vs 论文中的实际证据强度）
```

- **paper-analyst**（flyer-Li）：5 种分析模式，quick 最省时，带反幻觉溯源标注。
- **academic-paper-review**（字节 DeerFlow）：结构化评审，专治 overclaim。

## 场景 B：长期跟一篇 —— 深度精读

```text
Papers/xxx.pdf
   │
   ▼ ①
/skill:deeppapernote <论文标题/DOI/PDF路径>
   └── 入库：Obsidian 风格深读笔记（结构化、证据锚定、图占位）
   │
   ▼ ② （机制还是糊）
/skill:paper-analyzer
   └── 6 轮强制工作流重讲一遍，公式渲染 + Mermaid 图，输出 HTML 长文
   │
   ▼ ③ （生医方向必加）
/skill:scientific-critical-thinking
   └── 实验设计效度、偏倚/混杂、统计解读、GRADE/Cochrane 证据分级
   │
   ▼ ④
/skill:academic-paper-reviewer
   └── 压力测试：5 席评审团（期刊匹配 + 3 位领域评审 + 魔鬼代言人）
   │
   ▼ ⑤ （想分享时）
/skill:paper-slide-deck
   └── 风格化 slide 图片（17 种美学风格，适合社交分享，不保证公式精确）
```

## 归档流程（整理入库）

1. 新论文先放 `Papers/_Inbox/`。
2. `/skill:paper-analyst` quick 判断：值不值得读、属于哪个分类。
3. 规范化命名 `首作者-Year-短标题.pdf`，移入分类目录（如 `Papers/RAG/`）；需要时新建子分类目录。
4. `/skill:deeppapernote` 生成深读笔记，vault = `Notes/`，papers_dir = 分类子路径（如 `RAG`），语言 `zh-CN`。
5. 更新 `Notes/INDEX.md` 总台账和对应 `Notes/<分类>/_index.md`，状态置为 `notes-done`。
6. 中间产物放在 `.work/deeppapernote/`，可定期清理。

约定：`Papers/` 只放 PDF 原文；`Notes/` 是 Obsidian vault（每篇论文一个自包含目录：笔记 + PDF 副本 + images），目录树与 `Papers/` 镜像；交叉分类用笔记 tags，不复制文件。

## 使用约定

1. **入库优先**：凡是要长期跟的论文，先跑 deeppapernote，后续所有评审输出都追加到同一笔记，方便回看。
2. **输入路径**：给 skill 的论文路径一律相对项目根（如 `Papers/RAG/xxx.pdf`）。
3. **两两职责边界**，避免混用：
   - paper-analyst = 快速理解；paper-analyzer = 深度重讲（长文 HTML）
   - academic-paper-review = 单一评审视角 + Claim-Evidence 表；academic-paper-reviewer = 多角色评审团压力测试
   - paper-slide-deck = 视觉分享图（公式会被"烤进"图片，不精确）；若要组会/答辩级别精确 PPT，需另装 scholar-slides（未装）
4. **改了 skill 或新装**：会话内执行 `/reload` 生效。
