---
title: Game AI Pro
category: reference
tags: [游戏AI, Game AI Pro, 架构, 寻路, 战术, 感知, 赛车, Steve Rabin]
sources: [Game AI Pro (Steve Rabin 编, CRC Press 2014)]
confidence: high
synthesized: 2026-06-23
summary: Game AI Pro 第1卷按书提炼。业界一线游戏AI工程师文集，覆盖架构、寻路、战术、感知、赛车5大部分39章，含行为树/Utility/HTN奠基性章节。
---

# Game AI Pro

> 单书参考。Steve Rabin 编，CRC Press 2014，612 页，39 章。游戏 AI 工业界奠基性实战文集。

## 概述

Game AI Pro 系列的开山之作，汇集业界一线游戏 AI 工程师的实战经验。每章独立成篇（不同作者、不同案例），覆盖游戏 AI 的五大领域。本卷奠定了行为树、Utility 理论、HTN 规划器、NavMesh 寻路、战术位置选择等技术的工业标准。

原著章节见 [Game AI Pro 官网](http://www.gameaipro.com/)（CRC Press 免费发布的业界游戏 AI 实战文集）。本参考综合其架构篇核心章节（行为树/Utility/HTN/架构等）。

## 五大领域与核心章节

### Part I General Wisdom（通用智慧，Ch1-3）

随机性技术（Gaussian/filtered randomness/Perlin noise）等 AI 基础工具。

### Part II Architecture（架构，Ch4-16）⭐ 已提取

本卷最核心的部分，奠定现代游戏 AI 决策架构：

| 章 | 主题 | 作者 | 关联 concept |
|---|---|---|---|
| 4 | 行为选择算法概览 | Dawe 等 | [[游戏AI架构\|游戏AI架构]] |
| 5 | 架构通用技巧 | Dill | [[游戏AI架构\|游戏AI架构]] |
| 6 | 行为树入门套件 | Champandard | [[游戏AI行为树\|游戏AI行为树]] |
| 7 | 脚本中的真实行为树 | Dawe | [[游戏AI行为树\|游戏AI行为树]] |
| 9 | Utility 理论入门 | Graham | [[效用理论\|效用理论]] |
| 10 | 行为树融合 Utility | Merrill | [[效用理论\|效用理论]] |
| 11 | 决策的响应性与深思 | Côté | [[游戏AI架构\|游戏AI架构]] |
| 12 | HTN 规划器实例 | Humphreys | [[HTN层次任务规划\|HTN层次任务规划]] |
| 13 | 多单位战斗分层规划 | van der Sterren | [[HTN层次任务规划\|HTN层次任务规划]] |
| 14 | AI 细节层次控制(LOD) | Sunshine-Hill | [[游戏AI架构\|游戏AI架构]] |
| 15 | 运行时编译C++ | Binks 等 | （工具篇） |
| 16 | 脚本与AI | Lewis | [[游戏AI架构\|游戏AI架构]] |

### Part III Movement and Pathfinding（移动与寻路，Ch17-25）

A* 优化、搜索空间表示、NavMesh 生成、预计算寻路、流场寻路、人群模拟、动画驱动移动。→ 批次⑥c 待编译。

### Part IV Strategy and Tactics（战术与策略，Ch26-30）

战术位置选择（查询语言）、战术寻路、灵活攻击管理（Killzone 3 分层 AI）、神经网络威胁响应。→ [[战术推理\|战术推理]]，批次⑥c 待补。

### Part V Agent Awareness（感知与知识表示，Ch31-38）

Crytek Target Tracks 感知系统、2D 潜行 NPC 意识、知识表示系统、社交动力学、背景角色、Alibi 生成。→ 批次⑥b 待编译。

### Part VI Racing AI（赛车AI，Ch38-47）

赛车架构、赛道表示、PID 控制器、橡皮筋系统。→ 后续可选编译。

## 核心方法论

- **行为树（BT）**：从根节点每帧遍历的分层决策树，Sequence/Selector 组合复杂逻辑，Blackboard 共享状态。当前工业最广泛架构。
- **Utility 理论**：多因素加权评分选行为，曲线映射原始输入到 0-1 效用值，噪声注入避免确定性。
- **HTN 规划器**：层次任务网络，把复杂目标分解为原始任务序列，适合多步骤战术规划。
- **LOD 控制**：按 AI 与玩家距离调整决策频率和复杂度，性能优化的核心手段。

## 关键术语

| 术语 | 说明 |
|---|---|
| Behavior Tree (BT) | 行为树 |
| Utility / Consideration | 效用 / 考虑因素 |
| HTN (Hierarchical Task Network) | 层次任务网络 |
| Blackboard | 黑板（节点间共享数据） |
| NavMesh | 导航网格 |
| LOD (Level of Detail) | 细节层次 |
| Tick | 行为树的单次遍历 |

## 相关 concepts

- [[游戏AI行为树|游戏AI行为树]]
- [[效用理论|效用理论]]
- [[HTN层次任务规划|HTN层次任务规划]]
- [[游戏AI架构|游戏AI架构]]
- [[游戏AI架构体系|游戏AI架构体系]]
- [[战术推理|战术推理]]
- [[博弈树搜索|博弈树搜索]]
- [[智能的错觉|智能的错觉]]
