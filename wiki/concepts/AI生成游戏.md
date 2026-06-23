---
title: AI生成游戏
category: concept
tags: [小游戏开发, AI生成游戏, LLM, Generative Reroll, ChatGPT, Claude, ANGELINA]
related: [单按钮游戏设计, 程序化生成, 迭代设计流程]
sources: [generation-can-be-self, generation-ai-new-puzzle, generation-ai-combine-tags, generation-can-ai-create]
confidence: medium
synthesized: 2026-06-23
summary: AI生成游戏的演进（ANGELINA/TGM→LLM）；Generative Reroll开发法（LLM生成创意+代码，人工修平衡）；LLM能力边界——擅长创意与分步代码，仍难评估质量与调平衡。
---

# AI生成游戏

> 第9章综合。原书见 [第9章](../references/第9章-AI生成游戏.md)。

## 定义

**AI 生成游戏**指用 AI（早期 ANGELINA/遗传算法，近期 LLM）自动生成游戏创意、规则乃至代码。作者的梦想是"一台生成小游戏的机器"——开发者玩它生成的游戏，采纳有趣的、丢弃无趣的，仅靠此过程创作。前提是人类事后审选，故多数产物无趣也无妨，关键是生成时**机制足够多样**。

## 历史演进

### 前 LLM 时代：ANGELINA / TGM
通过随机组合 **Toggleable Game Mechanics (TGM)**（按键翻转变量符号/倍半）生成新游戏，用遗传算法选合适的 TGM 组（适应度=玩家在关卡移动的广泛程度）。局限：产出变化范围有限，是否算"新游戏"存疑。

### LLM 时代：ChatGPT / Claude
LLM（尤其 Claude 3.5 Sonnet + 前置知识）打破局限，能生成独特规则与可分步实现的代码。

## 三种 LLM 实践方法

### 方法一：构思益智游戏（ChatGPT）
让 ChatGPT 设计新逻辑益智游戏（如 Sumplete/Magnet Blocks）。
- **难点**：保证新颖性几乎不可能（Sumplete 实为已有的 Summer）；ChatGPT 难生成精确可玩规则，需人工精炼。
- **适用**：逻辑益智游戏有固定风格，是头脑风暴新规则的好母题。

### 方法二：组合机制标签（ChatGPT）
把 [[第3章-创意构思]] 的机制标签（player: rotate / weapon: bounce 等）喂给 ChatGPT，让它组合标签 + 描述新单按钮游戏。产出如 Pinball Panic（弹球+钉+敌）、Springy Strings（弹簧+零重力）等创意。
- **适用**：头脑风暴创意基础；但 ChatGPT 无法准确实现代码。

### 方法三：Generative Reroll 开发法（Claude，核心方法论）
用 Claude 3.5 Sonnet + 前置知识（构思方法、单按钮动作、自制游戏规则），**分步实现**：

**四步 prompt 法**（提升代码正确率）：
1. 用游戏环境/核心机制/玩家交互/挑战**组织规则**。
2. 详细描述游戏对象行为（属性/状态/形状/颜色/移动/操控）。
3. 给 **JavaScript 骨架代码**。
4. 用库**补全为可玩游戏**。

并附自制游戏示例。反复"reroll"直到得到有趣行为的版本。

**案例**：主题"脆弱柱子" → Claude 产出 Pillar Paraglider（按键上升+圆形冲击波毁柱）→ 人工改进为 WAVY BIRD（冲击波加方向性、按毁柱部分计分+连击倍数、放弃原核心机制改走"破坏"路线）。

**分工**：**LLM 生成创意 + 代码；人类修 bug、改规则使其可玩、加风险回报的 game over 与计分、调难度曲线**。看似没省工，但 LLM 代码偶有有趣行为，适合做"创意基础的 toy"。

## LLM 能力边界（核心论点）

| 能力 | 现状（2024 视角） |
|---|---|
| 生成新创意 | **改善中** — Claude 3.5 Sonnet + 前置知识提高独特规则概率 |
| 实现代码 | **分步可** — 直转代码仍难，分步细化可提升正确率 |
| 评估质量/调平衡 | **仍难** — 判断"功能正常/风险回报合适/是否好玩"需计算机自玩+分析，AI 能否理解"爽快感"存疑 |
| 改进规则/操控/调参 | **仍难** — 理论可用强化学习（质量为奖励），能否恰当改进存疑 |

> [!warning] 前景判断
> LLM 演进若持续，创意与代码质量会提升。但**游戏平衡调整、趣味性评估等创造性环节，可预见未来仍需人类介入**。截至 2024 年有"LLM 性能提升放缓"的印象。

## 应用

- **创意生成器**：把机制标签/构思方法做成知识库喂 LLM，作为头脑风暴伙伴。
- **Generative Reroll 流程**：LLM 生成 → 人工筛选有趣行为 → 人工修平衡完成。享受"游戏开发"与"prompt 改进"的并行过程。
- **定位 LLM 角色**：只让它做擅长的事（创意 + 代码），平衡与趣味评估仍靠人。

## See Also

- [[单按钮游戏设计|单按钮游戏设计]] ([单按钮游戏设计](单按钮游戏设计.md)) — Generative Reroll 的常见目标类型
- [[程序化生成|程序化生成]] ([程序化生成](程序化生成.md)) — 程序化生成的极致形态
- [[迭代设计流程|迭代设计流程]] ([迭代设计流程](迭代设计流程.md)) — Generative Reroll 本质是 AI 驱动的迭代/原型流程（生成→筛选→修平衡）
