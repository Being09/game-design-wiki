# 操作日志

## [2026-06-23] init | 建立游戏设计知识库骨架
- 建立目录结构（raw/books、wiki/{concepts,references,topics}、output、_tools）
- 写入 config.md（wiki 元信息）

## [2026-06-23] ingest + compile | 批次① 设计哲学与机制完成
- raw 摄取 4 本（游戏设计艺术 11805 行、体验引擎 7409 行、游戏机制 5570 行、游戏迭代设计 4575 行），合并分卷+中度清理
- 编译 4 篇 references（单书提炼，覆盖全部章节）+ 8 篇 concepts（跨书综合）
- 建立 topics 领域导航页《设计哲学与机制》
- 建立派生索引（raw/books、wiki 三层），计数全部一致
- 修正：《游戏机制》实际方法论为突现/渐进+内部经济+Machinations+设计模式（非 MDA），concepts 据此命名
- 领域知识闭环可用

## [2026-06-23] ingest + compile | 批次② 数值与经济完成
- raw 摄取 3 本：游戏数值百宝书（2128 行，至第4章）、平衡掌控者（epub→6752 行，全书6章）、虚拟经济学（1858 行，至第9章）
- 给 extract_book.py 加 epub 分派（pandoc）+ 内联 HTML 清理
- 编译 3 篇 references + 7 篇 concepts（数值平衡/战斗数值设计/数值公式体系/虚拟经济系统/货币与市场/玩家动机与留存/成长曲线设计）
- topics 导航页《数值与经济》
- 跨批交叉链接：游戏经济结构→虚拟经济系统/货币与市场；情感工程→玩家动机与留存；游戏机制设计→数值平衡
- 索引计数一致：raw=7, references=7, concepts=15, topics=2

## [2026-06-23] ingest + compile | 批次③ 数学与编程模拟完成
- raw 摄取 2 本：代码本色（12079 行，全书11章完整）、游戏中的数学与物理学第2版（13613 行，5部分26章+附录完整）
- 编译 2 篇 references + 7 篇 concepts（物理模拟/向量与运动/概率与随机/群体行为/游戏数学基础/几何与碰撞/矩阵与变换）
- topics 导航页《数学与编程模拟》
- 索引计数一致：raw=9, references=9, concepts=22, topics=3

## [2026-06-23] ingest + compile | 批次④ 博弈论完成 — 全库竣工
- raw 摄取 2 本：博弈论（13975 行，5部分14章完整）、斯坦福算法博弈论二十讲（6591 行，20讲完整）
- 编译 2 篇 references + 6 篇 concepts（纳什均衡/策略式博弈/扩展式博弈/机制设计/拍卖理论/算法博弈论）
- topics 导航页《博弈论》
- 跨批交叉链接：游戏机制设计→算法博弈论
- 全库最终计数：raw=11, references=11, concepts=28, topics=4
- **10 本书（11个档案）全部编译完成，知识库闭环**

## [2026-06-23] init | （原 joys-of-small-game-dev wiki）初始化知识库

- 在 `References/joys-of-small-game-dev/` 建立目录结构（raw/articles、wiki/references、wiki/concepts、wiki/topics、output、_tools）。
- 写 config.md（元信息 + CC BY 4.0 署名 ABA Games）、spec.md（设计决策：GitHub 原始 markdown 来源、深度编译、25篇逐篇摄取、中文编译层）。
- 写 _tools：articles_meta.json（25 篇）、ingest.py（摄取 markdown + frontmatter）、build_index.py（重建索引）。
- 复用 `References/game-design/` 与既有写作知识库先例。

## [2026-06-23] ingest | （原 joys-of-small-game-dev wiki）摄取 25 篇原文

- clone 仓库 `abagames/joys-of-small-game-development-en` 到 `_source_repo/`。
- 运行 `ingest.py`，把 25 篇原始 markdown 摄取到 `raw/articles/`（每篇含 frontmatter：title/title_zh/author/source_url/license CC BY 4.0/chapter/tags/summary）。
- 保留英文原文 + 公式 + 代码 + 脚注 + 图片链接（轻度清理，raw 为保真档案）。
- 摄取后删除 12MB 的 `_source_repo/` 中间产物。

## [2026-06-23] compile | （原 joys-of-small-game-dev wiki）深度编译（中文）

- 写 `wiki/references/` 10 篇按章提炼：第1-9章 + 结语，每篇含概述/核心论点/方法/术语/相关 concepts。
- 写 `wiki/concepts/` 7 篇跨章综合：小游戏哲学、单按钮游戏设计、难度曲线设计、约束激发创意、Juiciness与游戏手感、程序化生成、AI生成游戏。
- 写 `wiki/topics/实践方法论.md` 主题导航页，串联全书核心主题与跨库关联。
- 标注 confidence（high/medium），双链规范（[[wikilink]] + markdown）。

## [2026-06-23] merge | 合并 joys-of-small-game-dev → game-design

- 把 joys-of-small-game-dev wiki 整体并入 game-design，成为统一「游戏设计知识库」（理论 + 实践）。
- raw 层：joys 的 `raw/articles/`（25 篇）移入 game-design 的 `raw/articles/`，与 `raw/books/`（11 本）并存保留双粒度，内容未改（不可变）。
- wiki 层：joys 的 10 篇 references、7 篇 concepts、1 篇 topics 移入 game-design 对应目录。
- 重建三层索引（build_index.py）：references=21、concepts=35、topics=5；微调脚本描述文案以涵盖两种粒度。
- 补建交叉链接：难度曲线设计 ↔ 数值平衡（双向）；约束激发创意→迭代设计流程/原型设计；AI生成游戏→迭代设计流程；程序化生成→概率与随机；Juiciness与游戏手感→游戏体验设计/情感工程（单向）。
- 元信息合并：joys 的 CC BY 4.0 授权与来源并入 config.md「来源②」章节 + 新增「批次⑤ 实践方法论」；joys 的 log 3 条历史记录追加到本文件（标注原 wiki 出处）。
- 删除 `References/joys-of-small-game-dev/` 源目录。
- 合并后全库：raw=36（11 books + 25 articles）、references=21、concepts=35、topics=5。

## [2026-06-23] ingest + compile | 批次⑥a 游戏AI（NPC决策核心）完成

- 新增来源③：Game AI Pro 系列（Steve Rabin 编，CRC Press，7卷3017页），业界游戏AI实战文集。
- 新建 PDF 提取工具链 `extract_game_ai_pdf.py`：pypdf 解析目录(TOC)→按章拆分→中度清理→注入 frontmatter（含章号/标题/作者/来源书/页码/出处）。修正了范围末章边界丢失的 bug + Pro 3 的 glob 误匹配。
- raw 层提取 17 章：Pro 1 架构篇（Ch4-16 共12章）+ Pro 2 规划与搜索（Ch13、Ch22-25 共5章）。提取质量验证通过（偏移+30稳定、英文文本完整）。Pro 3 目录格式特殊（Tab分隔无页码）留待后续批次。
- concepts 层迁入 8 篇（混合策略）：游戏AI行为树、效用理论、游戏AI架构、HTN层次任务规划、博弈树搜索、智能的错觉、游戏AI架构体系、战术推理。由 skill-project 骨架（中文深度编译）+ PDF 章节出处回填而成（写迁移脚本 migrate_skills_b6a.py 批量转换 frontmatter/See Also/出处）。
- 补迁架构体系+战术推理消除交叉死链（被行为树/效用/HTN引用）。
- 交叉链接：博弈树搜索↔纳什均衡（实践↔理论桥接，批次④延伸）；智能的错觉→游戏体验设计；各 concept 间 See Also 闭环。
- 写 references/Game AI Pro.md（Pro 1 按书提炼，39章5大领域）+ topics/游戏AI.md（领域导航，含全景图/决策矩阵/交叉关联）。
- 扩展 build_index.py：新增 build_articles_index（兼容 joys 文章与 Pro 章节两种 frontmatter）。
- 全库计数：raw=53（11 books + 42 articles）、references=22、concepts=43、topics=6。
- 批次⑥b/⑥c/⑥d（感知社交/寻路战术/PCG模拟）待后续推进。

## [2026-06-24] compliance | 合规化清理（侵权消除 + 个人信息脱敏）

**背景**：raw/books/ 含 11 本商业教材全文（侵权，超出 fair use），且全文散落出版社/译者邮箱、手机号、QQ 群号、微信扫码等个人信息。原 config 声称的 `.gitignore` 实际不存在。

**操作（用户确认全删 raw + 充实 wiki）**：

1. **阶段A 充实 wiki 层**：删除 raw 前先充实薄文，减少损失。所有 ≤50 行 concept（约 24 篇）补"具体例子"段（公式/矩阵/代码/数值表/案例），使其不依赖 raw 深链即可独立可读。结语、第1/6章 reference 也补实操细节。充实只基于已有综合内容扩写，不复制原文（避免换地侵权）。concepts 总行数 3902→4698。

2. **阶段B 链接迁移**：把所有 `../../raw/` 深链改为外部来源链接——11 单书 references 的 source frontmatter + 正文行改为作者/出版社/豆瓣；11 ABA 章节 references 改为 CC BY 4.0 仓库链接；Game AI Pro references 改为 gameaipro.com；6 topics 的 raw/books 行改为外部书目表。共迁移 25 文件，wiki/ 中零 raw 引用残留。

3. **阶段C 删除**（不可逆）：`raw/`（56 文件 8.8MB）、`_tools/`（5 脚本）、`_legacy-joys-spec.md`、`spec.md`、迁移脚本全删。设计文档归档至 `docs/superpowers/specs/`。

4. **阶段D PII 兜底扫描**：保留文件（wiki/output/config/README/log）全文扫描手机号/邮箱/QQ群/微信扫码/身份证，零命中。设计文档中的 PII 示例值已脱敏。

5. **阶段E 文档收尾**：更新 config.md（删 raw/books 描述、改来源为外部链接、加合规化说明）、README.md（删 raw/_tools 结构、改"不含全文"说明）、各级 _index.md 更新日期与计数。创建 `.gitignore` 护栏防未来误摄入全文。

**最终状态**：全库自闭环——43 concepts + 22 references + 6 topics，无原文档案、无侵权全文、无个人信息，各文章标注外部来源链接可溯源。计数与实际一致：concepts=43、references=22、topics=6、output=2。
