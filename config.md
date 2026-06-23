---
title: 游戏设计知识库
slug: game-design
description: 游戏设计教材理论 + 小游戏独立开发实践的深度编译知识库
mode: local
created: 2026-06-23
---

# 游戏设计知识库

合并两个来源：**教材理论**（10 本书）+ **独立开发实践**（ABA Games《Joys of Small Game Development》）。理论偏体系（透镜/MDA/数值平衡/博弈论），实践偏经验（单按钮/难度公式/程序化生成/juiciness），二者在 concepts 层交叉互补。

## 来源

本知识库综合自三类来源，**原文均未收录**（合规化清理后 raw 全文档案已删除，仅保留中文综合编译层）。所有 references/topics 标注外部来源链接以供溯源。

### 来源①：游戏设计与AI教材（理论）

17 本业界权威教材（10 本游戏设计 + Game AI Pro 系列 7 卷）。教材为商业出版物，本库仅做结构化提炼与跨书综合，**不含全文**。各 reference 标注作者/出版社/豆瓣链接供查阅原著。

### 来源②：小游戏开发之乐（实践）

ABA Games（keita）的《Joys of Small Game Development》（HonKit 长文）。

- 许可证：[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.en)
- 署名：ABA Games (keita)
- 原文：<https://abagames.github.io/joys-of-small-game-development-en/>
- 仓库：<https://github.com/abagames/joys-of-small-game-development-en>
- 作者 keita 是高产独立开发者，已制作 350+ 款小游戏（曾一年做 139 款），本书是其小游戏开发方法论的系统总结。
- 编译层为中文综合，标注来源；原文见上述外部链接。

### 来源③：Game AI Pro 系列

Steve Rabin 编，CRC Press 出版，业界一线游戏 AI 工程师实战文集（含 TLOU/FFXV/Killzone 等 3A 案例）。原著章节免费发布于 <http://www.gameaipro.com/>，本库做中文综合提炼。

## 领域划分与推进批次

- 批次① 设计哲学与机制：游戏设计艺术、体验引擎、游戏机制、游戏迭代设计
- 批次② 数值与经济：游戏数值百宝书、平衡掌控者、虚拟经济学
- 批次③ 数学与编程模拟：代码本色、游戏中的数学与物理学
- 批次④ 博弈论：博弈论、斯坦福算法博弈论二十讲
- **批次⑤ 实践方法论**：ABA Games《Joys of Small Game Development》（单按钮/难度曲线/约束/程序化生成/AI生成游戏/juiciness/小游戏哲学）
- **批次⑥ 游戏AI**：Game AI Pro 系列
  - ⑥a NPC决策核心（已完成）：行为树/Utility/架构/HTN/博弈搜索/智能错觉/架构体系/战术推理
  - ⑥b-⑥d（待补）：感知社交/寻路战术/PCG模拟

## 结构

- `wiki/references/` — 单书/单章的结构化参考（标注外部来源链接）
- `wiki/concepts/` — 跨书/跨章综合主题文章
- `wiki/topics/` — 领域导航索引页
- `output/` — 应用产物（使用案例等）

> [!note] 合规化说明
> 原始全文档案（`raw/`）已于 2026-06-24 合规化清理删除——11 本商业教材全文涉及侵权、全文档案含出版社/译者联系方式等个人信息。删除前已充实 wiki 综合层（补具体公式/案例/步骤）并迁移所有深链为外部来源链接（出版社/豆瓣/CC BY 仓库/gameaipro.com），使知识库自闭环且不依赖原文档案。
