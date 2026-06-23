---
title: 合规化清理与 raw 删除 — 设计 spec
slug: compliance-cleanup
status: draft
created: 2026-06-23
---

# 合规化清理与 raw 删除 — 设计 spec

## 1. 背景与目标

游戏设计知识库（`game-design-wiki`）的 `raw/` 目录存在两类合规问题：

1. **侵权**：`raw/books/`（11 个文件，7.9MB）是商业教材**全文**，超出 fair use。
2. **PII**：散落出版社/译者邮箱、手机号、QQ 群号、
   微信扫码入口等。这些 PII 绝大多数嵌在 books 全文中，随 books 删除而消失。

`raw/articles/`（42 篇）虽合规（25 篇 CC BY 4.0 + 17 章 Game AI Pro 免费发布），
用户决定**一并删除**，以彻底消除全文档案。但为减少 raw 删除对 wiki 综合层的可验证性损失，
**删除前先充实 wiki 层**使其自闭环。

### 1.1 关键决策（已与用户确认）

| # | 决策点 | 选择 |
|---|---|---|
| 1 | raw 删除范围 | **全删**（books + articles，整目录删除） |
| 2 | 充实工作量 | **全量充实所有薄文**（≤50 行 concept + 薄 references） |
| 3 | `_tools/` 与旧 spec | **全删**（提取脚本 + `_legacy-joys-spec.md`） |
| 4 | `spec.md` | 标记废弃后删除 |
| 5 | `raw/_index.md` | 随 `raw/` 整体删除（不留说明页） |
| 6 | 外部溯源链接 | **全部加**（books→出版社/豆瓣；ABA→GitHub CC BY；GAI Pro→gameaipro.com） |

### 1.2 不可逾越的边界

- **不把 raw 全文搬进 wiki**：充实是把"已综合的知识"写得更具体（公式/案例/步骤），
  而非换地存全文——那只是转移侵权，不是消除。
- **PII 零容忍**：删除后对 `wiki/` 与 `output/` 做全文 PII 扫描兜底。
- **log.md 只追加**：合规化清理记一条新条目，不删改历史。

---

## 2. 现状盘点

### 2.1 raw/（待全删）

- `raw/books/`：11 本（代码本色/体验引擎/博弈论/平衡掌控者/斯坦福算法博弈论二十讲/
  游戏中的数学与物理学/游戏数值百宝书/游戏机制/游戏设计艺术/游戏迭代设计/虚拟经济学）
- `raw/articles/`：42 篇（25 篇 ABA Games CC BY 4.0 + 17 章 Game AI Pro）
- `raw/_index.md`、`raw/books/_index.md`、`raw/articles/_index.md`：3 个派生索引

### 2.2 wiki/（保留并充实）

- `concepts/`：43 篇，其中 ≤50 行约 24 篇（待充实重点）
- `references/`：22 篇（11 单书 + 10 ABA 章节提炼 + 1 Game AI Pro）
- `topics/`：6 篇领域导航页
- 深链依赖：22 篇 references 的 `source`/`原文见` + 6 topics 的 `raw/books/` 行

### 2.3 其他待处理

- `_tools/`：`extract_book.py`、`extract_game_ai_pdf.py`、`build_index.py`、`books_meta.json`、
  `articles_meta.json` 等（全删）
- `_legacy-joys-spec.md`：旧 spec（删）
- `spec.md`：当前 spec（标记废弃后删）
- `config.md` / `README.md`：需更新描述
- 无 `.gitignore`、非 git 仓库（删除不可逆，需谨慎）

---

## 3. 实施阶段

按依赖顺序执行，每阶段完成后可独立验证。

### 阶段 A：充实 wiki 层（核心工作量）

**目标**：薄文补具体公式/案例/步骤，使其不依赖 raw 深链即可独立可读。

充实标准（每篇应含）：
- 可独立理解的定义（不依赖点开 raw）
- 至少 1 个具体例子 / 公式 / 数据表
- 可操作的方法或步骤
- 外部溯源链接（见阶段 B）

**充实对象**：
1. `concepts/` 中 ≤50 行的约 24 篇：透镜方法、Machinations建模、策略式博弈、原型设计、
   扩展式博弈、情感工程、概率与随机、游戏体验设计、游戏经济结构、矩阵与变换、迭代设计流程、
   几何与碰撞、成长曲线设计、拍卖理论、游戏数学基础、群体行为、货币与市场、向量与运动、
   战斗数值设计、玩家动机与留存、算法博弈论、数值公式体系、纳什均衡、虚拟经济系统 等。
2. `references/` 薄篇：第1-9章 + 结语（多数 22-92 行），每篇补原文关键论点的具体化。
   11 单书 references 已较扎实（76-125 行），按需微调。

**方法**：逐篇 Edit 扩写，单次 Write ≤200 行（wiki-manager 原则 9）。
**注意**：不复制原文大段，只把已综合的知识写得更具体。

### 阶段 B：链接迁移（raw 深链 → 外部源）

**目标**：消除所有 `../../raw/` 相对链接，改为外部来源。

1. **22 篇 references**：
   - frontmatter `source: "[书名](../../raw/books/书名.md)"` → 删除或改为外部
   - 正文 `> 单书快速参考。原始全文见 [书名](../../raw/books/书名.md)。` →
     `> 综合自《书名》（作者，出版社，年份）。原文见 [外部链接]。`
2. **6 篇 topics**：
   - `raw/books/...` 行 → 「来源书目（外部）」表，列书名/作者/出版社/链接
3. **外部链接清单**：
   - 教材：出版社页或豆瓣读书条目（出版社：人民邮电/机械工业/电子工业 等）
   - ABA Games：`https://github.com/abagames/joys-of-small-game-development-en`
     + CC BY 4.0 声明
   - Game AI Pro：`http://www.gameaipro.com/`

### 阶段 C：删除 raw + 清理脚本

1. 删除整个 `raw/` 目录（books/articles/3 个 index）
2. 删除整个 `_tools/` 目录
3. 删除 `_legacy-joys-spec.md`
4. 删除 `spec.md`（先在 log 中记录其废弃原因）

### 阶段 D：PII 兜底扫描

对保留的 `wiki/` + `output/` + `config.md` + `README.md` 做全文 PII 扫描：
- 手机号 `\b1[3-9]\d{9}\b`
- 邮箱（出版社/个人）
- QQ 群号、微信号、扫码入口
- 身份证号、银行卡号（兜底）

发现即脱敏或删除。**注意区分**：编辑性内容（如《虚拟经济学》正文讲腾讯 QQ 的经济史）
在 wiki 综合层若被引用，属正常知识内容，不是 PII。

### 阶段 E：文档与索引收尾

1. **config.md**：删除「来源① raw/books 全文档案」描述；改「知识已综合进 wiki，
   原文见外部链接」；保留领域划分与批次历史（作为编译过程记录）。
2. **README.md**：删 raw 相关结构说明；改「知识来源」为带外部链接的书目清单；
   更新目录结构图；更新计数（去掉 raw/、_tools/）。
3. **各级 `_index.md`** 重建：去掉 raw 相关注释，更新 wiki/_index.md 的子目录计数。
4. **`log.md`** 追加：`## [2026-06-23] compliance | 合规化清理`，记录删除范围与原因。
5. **`.gitignore`** 创建：排除未来误摄入的常见侵权模式（`raw/books/`、PDF 等）作为护栏。

---

## 4. 验证标准

完成后必须满足：

- [ ] `raw/` 目录不存在
- [ ] `_tools/`、`_legacy-joys-spec.md`、`spec.md` 不存在
- [ ] `wiki/` + `output/` + `config.md` + `README.md` 中无任何 `raw/` 路径引用
- [ ] PII 扫描：保留文件中零命中（手机号/邮箱/QQ群/微信扫码）
- [ ] 所有 references 含外部来源链接
- [ ] 所有 topics 含外部来源书目表
- [ ] 各级 `_index.md` 计数与实际文件数一致
- [ ] `log.md` 新增合规化清理条目
- [ ] `.gitignore` 已创建
- [ ] 充实后的薄文均含定义+具体例子/公式+方法步骤+外部溯源

---

## 5. 风险与回退

- **不可逆**：非 git 仓库，raw 删除后无法恢复。阶段 C 执行前再次确认。
- **充实质量**：充实依赖对已有 wiki 综合内容的扩写，不引入新事实（避免在无源情况下
  编造）。若某 concept 缺乏足够信息充实，保持原样并标注，不强凑。
- **外部链接失效**：使用稳定来源（出版社官网、GitHub、gameaipro.com），避免短链。
