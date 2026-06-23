---
title: 第9章 小游戏能自我生成吗？
category: reference
chapter: 9 AI 生成
sources: [generation-can-be-self, generation-ai-new-puzzle, generation-ai-combine-tags, generation-can-ai-create]
tags: [小游戏开发, AI 生成游戏, LLM, ChatGPT, Claude, Generative Reroll, ANGELINA]
related: [AI 生成游戏]
confidence: medium
synthesized: 2026-06-23
summary: AI 生成游戏演进（ANGELINA/TGM→LLM）；用 ChatGPT 构思益智游戏与组合机制标签；Generative Reroll 开发法（LLM生成创意+代码，人工修平衡）；LLM 能力边界。
---

# 第9章 小游戏能自我生成吗？

> 单章快速参考。综合自 ABA Games《Joys of Small Game Development》（CC BY 4.0）。原文见 [项目仓库](https://github.com/abagames/joys-of-small-game-development-en) / [在线阅读](https://abagames.github.io/joys-of-small-game-development-en/)。

## 概述

作者的梦想：一台自动生成小游戏的机器——开发者玩它生成的游戏，采纳有趣的、丢弃无趣的，仅靠此过程创作游戏。前提是**人类事后审选**，故多数产物无趣也无妨，关键是生成时**机制足够多样**。本章追溯 AI 生成游戏的历史，重点讲 LLM（ChatGPT/Claude）带来的突破与实践方法。

## AI 生成游戏的历史

**ANGELINA 项目**：通过随机组合机制生成新游戏——按键时随机翻转变量（玩家位置、重力）的符号（+/-）或倍/半，这种"按键切换状态"的机制称 **Toggleable Game Mechanics (TGM)**，用于益智跳跃游戏从头到尾评估。用**遗传算法**从随机组合中选合适的 TGM 组（适应度=玩家到达终点前在关卡移动的广泛程度）。

**局限**：产出的游戏变化范围有限，是否算"新游戏"存疑。连人类构思新游戏都难，计算机更甚。**LLM（ChatGPT 为代表的聊天机器人 + 大语言模型）**有可能打破此局限。

## 用 ChatGPT 构思益智游戏

"ChatGPT invented its own puzzle game"——ChatGPT 被要求设计新逻辑益智游戏，提出 **Sumplete**（二维网格、行列标和、玩家划掉数字使行列和匹配）。但 Sumplete 其实早已存在（手机游戏 Summer）。**保证游戏新颖性很难**——人/AI 都不可能知晓所有游戏。

### 作者实践：让 ChatGPT 设计 5 个新益智游戏
prompt 要求：2D 网格、部分格有数字/符号、部分空、可能有墙分隔、玩家填空格达解。

产出如 **"Magnet Blocks"**（磁铁主题：网格有N/S磁铁，玩家填正负极方块使按极性相吸相斥，同极不可相邻）。规则略模糊，作者诠释为"N与N、S与S不可相邻，条形磁铁长≥3"并实现。虽有类似主题游戏（Magnets），规则不同——但非磁铁主题的相似规则集不能排除。

**经验**：逻辑益智游戏有固定风格，是 ChatGPT 头脑风暴新规则的好母题，但也因此"以为新的"很可能撞已有游戏。ChatGPT **难生成精确可玩的规则**——需人工把模糊提议精炼成具体规则，转成可玩代码更难。

## 教 AI 理解游戏机制（标签组合法）

把第 3 章的**机制标签**（player: rotate / weapon: bounce / field: pins / on_pressed: shoot 等）喂给 ChatGPT，让它**随机组合标签 + 描述新单按钮游戏**，产出 5 个创意：

| 游戏 | 选中标签 |
|---|---|
| Scaffold Jump | player: scaffold, on_pressed: jump |
| Pinball Panic | player: circle, weapon: bounce, field: pins, on_pressed: shoot |
| Springy Strings | player: string, player: bounce, field: gravity, on_pressed: jump |
| Wipeout Warriors | player: bar, field: water, obstacle: chase, weapon: wipe, on_pressed: jump |
| Color Conundrum | rule: match, rule: time limit, field: roughness, on_pressed: turn |

凭游戏名+标签+描述可挑有趣的作为创意基础（如 Pinball Panic 在满布钉与敌的场用反弹弹球灭敌）。但**ChatGPT 目前无法准确实现代码**——让它用 p5.js 实现 Game 1，输出的代码不能准确反映机制。**ChatGPT 适合头脑风暴，不适合直接产出代码**。

## Generative Reroll 开发法（核心方法论）

**用 Claude 3.5 Sonnet 等 LLM**，把构思方法、单按钮可分配动作、自制游戏规则作为前置知识喂入，分步实现：

**四步 prompt 法**（提升代码正确率）：
1. 用游戏环境/核心机制/玩家交互/挑战**组织游戏规则**。
2. 详细描述实现规则的游戏对象行为（属性/初始状态/形状/颜色/移动/单按钮操控）。
3. 给出实现规则与对象的 **JavaScript 骨架代码**。
4. 用库把骨架**补全为可玩游戏**。

并提供按此步骤实现的自制游戏示例。

### 案例：主题"脆弱柱子" → Pillar Paraglider → WAVY BIRD

Claude 原始产出（Pillar Paraglider）：按键让红色角色上升并发射圆形冲击波毁柱。问题：太易（连按即可清柱，到顶/底不 game over）、计分简陋（仅按距离）、未实现核心机制（本想平衡"上升 vs 保柱"）。

**人工改进**（→ WAVY BIRD）：
- 冲击波加方向性（窄角朝前），只毁直接命中部分而非整柱；触顶/底 game over。
- 按毁柱部分计分 + 连续毁奖励倍数，激励冒险。
- **放弃原核心机制**——"破坏"比"避免被破坏"更有趣。

最终成为"用冲击波在柱上开洞前进"的游戏，风险回报更平衡。

### Generative Reroll 的本质
**LLM 负责生成创意 + 实现代码；人类负责修 bug、改规则使其可玩、加风险回报的 game over 与计分、调难度曲线**。看似没省多少工，但 LLM 代码偶有有趣行为，适合做"创意基础的 toy"。

**关键**：享受"游戏开发"与"生成 prompt 改进"的**并行过程**；从 LLM 提议的游戏出发思考"如何让它有趣"，是与传统开发不同的乐趣。

## LLM 的能力边界（核心论点）

| 能力 | 现状 |
|---|---|
| 生成新创意 | **改善中**——Claude 3.5 Sonnet + 前置知识提高独特规则概率 |
| 实现代码 | **分步可**——直转代码仍难，分步细化可提升正确率 |
| 评估质量/调平衡 | **仍难**——判断"功能是否正常/风险回报是否合适/是否好玩"需计算机自己玩并分析，AI 能否理解"爽快感"存疑 |
| 改进规则/操控/调参 | **仍难**——理论上可用强化学习（质量为奖励），但能否恰当改进存疑 |

> [!warning] 前景判断（2024 年视角）
> LLM 演进若持续，创意与代码质量会提升，指出问题后或能改代码。但**游戏平衡调整、趣味性评估等创造性环节，可预见未来仍需人类介入**。截至 2024 年有"LLM 性能提升放缓"的印象。

详见 [[AI 生成游戏]]。
