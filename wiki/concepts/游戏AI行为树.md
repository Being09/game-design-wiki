---
title: 游戏AI行为树
category: concept
tags: [游戏AI, 行为树, BT, NPC决策, 行为选择, 黑板]
related: []
sources: [Game AI Pro Ch.6 行为树入门, Game AI Pro Ch.7 脚本行为树, Game AI Pro 3 Ch.9 行为树设计陷阱]
confidence: high
synthesized: 2026-06-23
summary: 行为树完整指南：Sequence/Selector/装饰器节点、黑板分层、遍历机制，含完整Python实现与反模式；当前游戏工业最广泛使用的AI决策架构。
---


# 行为树（Behavior Trees）


## 概述

行为树是一种用于控制游戏 AI 决策的分层树状结构。其核心设计理念是：

- **模块化**：每个节点封装一个独立行为或判断条件
- **可组合**：通过复合节点（Sequence、Selector）编排复杂的行为逻辑
- **响应式**：每帧从根节点开始遍历，天然支持对环境变化的快速反应
- **可调试**：节点状态（Success/Failure/Running）清晰可追踪

行为树是当今游戏工业中使用最广泛的AI决策架构，从 The Last of Us 到 Far Cry，从手游到3A大作，均有其应用。

### 适用场景

| 场景 | 说明 |
|------|------|
| NPC 日常行为编排 | 巡逻 → 警戒 → 追踪 → 攻击 的状态流转 |
| 敌人 AI 决策 | 根据距离/血量/掩体等条件选择行为 |
| Boss 战阶段管理 | 不同血量阶段切换攻击模式 |
| 角色技能连招 | 条件触发 + 动作序列的组合编排 |
| 单位控制（RTS） | 采集 → 建造 → 战斗 的工作流 |

---

## 核心概念

### 节点类型

行为树的核心节点分为四大类：

| 类别 | 节点类型 | 作用 | 返回值 |
|------|---------|------|--------|
| **复合节点** | **Sequence（序列）** | 顺序执行子节点，全部成功才返回成功 | Success / Failure |
|  | **Selector（选择器）** | 依次尝试子节点，第一个成功即返回 | Success / Failure |
|  | **Parallel（并行）** | 同时执行所有子节点 | 取决于完成策略 |
| **条件节点** | **Condition（条件）** | 检查黑板或世界状态 | Success / Failure |
| **动作节点** | **Action（动作）** | 执行具体行为 | Success / Failure / Running |
| **装饰节点** | **Decorator（装饰器）** | 修饰子节点行为（取反、限次、超时、循环） | 取决于子节点 |

#### Sequence vs Selector 的关键区别

```
Sequence（与门逻辑）：A 成功 → B 成功 → C 成功 = 整体成功
                       一旦失败就中断

Selector（或门逻辑）：A 失败 → B 失败 → C 成功 = 整体成功
                      一旦成功就中断
```

### 黑板（Blackboard）

黑板是行为树各节点间的共享数据存储机制：

```
Blackboard
├── self: { health, position, ammo, state }     ← AI自身状态
├── target: { position, distance, health }       ← 目标信息
├── world: { time, threat_level }               ← 世界状态
└── group: { allies_nearby, formation_pos }     ← 群体共享
```

**设计原则**：黑板按作用域分层 —— 个体级、组级、全局级，避免数据污染。

### 遍历规则

行为树的执行流程（tick）：

1. 每帧从根节点开始深度优先遍历
2. 返回 **Running** 的节点记录执行位置，下一帧从该位置继续
3. **Selector** 从左到右尝试，找到第一个可执行的子节点
4. **Sequence** 从左到右执行，遇到失败中断并返回

---

## 伪代码实现

```python
from enum import Enum

class Status(Enum):
    SUCCESS = 1
    FAILURE = 2
    RUNNING = 3

class Blackboard:
    def __init__(self):
        self.data = {}

    def get(self, key, default=None):
        return self.data.get(key, default)

    def set(self, key, value):
        self.data[key] = value


# ---- 核心节点实现 ----

class BehaviorTree:
    def __init__(self, root: Node, blackboard: Blackboard):
        self.root = root
        self.blackboard = blackboard

    def tick(self) -> Status:
        return self.root.tick(self.blackboard)


class Sequence:
    """顺序执行所有子节点，全部成功才返回成功"""
    def __init__(self, children: list):
        self.children = children
        self.running_index = 0  # 记录上次 Running 的位置

    def tick(self, bb: Blackboard) -> Status:
        for i in range(self.running_index, len(self.children)):
            status = self.children[i].tick(bb)
            if status == Status.RUNNING:
                self.running_index = i
                return Status.RUNNING
            elif status == Status.FAILURE:
                self.running_index = 0
                return Status.FAILURE
        self.running_index = 0
        return Status.SUCCESS


class Selector:
    """依次尝试子节点，第一个成功即返回"""
    def __init__(self, children: list):
        self.children = children
        self.running_index = 0

    def tick(self, bb: Blackboard) -> Status:
        for i in range(self.running_index, len(self.children)):
            status = self.children[i].tick(bb)
            if status == Status.RUNNING:
                self.running_index = i
                return Status.RUNNING
            elif status == Status.SUCCESS:
                self.running_index = 0
                return Status.SUCCESS
        self.running_index = 0
        return Status.FAILURE


class Condition:
    """检查黑板中的状态"""
    def __init__(self, check_fn):
        self.check_fn = check_fn

    def tick(self, bb: Blackboard) -> Status:
        return Status.SUCCESS if self.check_fn(bb) else Status.FAILURE


class Action:
    """执行具体行为"""
    def __init__(self, action_fn):
        self.action_fn = action_fn

    def tick(self, bb: Blackboard) -> Status:
        return self.action_fn(bb)


class Inverter:
    """装饰器：取反子节点结果"""
    def __init__(self, child):
        self.child = child

    def tick(self, bb: Blackboard) -> Status:
        status = self.child.tick(bb)
        if status == Status.SUCCESS:
            return Status.FAILURE
        elif status == Status.FAILURE:
            return Status.SUCCESS
        return Status.RUNNING


# ---- 使用示例 ----

# 创建黑板
bb = Blackboard()
bb.set("health", 50)
bb.set("enemy_visible", True)

# 构建行为树
tree = BehaviorTree(
    root=Selector([
        Sequence([
            Condition(lambda bb: bb.get("health") < 30),
            Action(lambda bb: print("逃跑!") or Status.SUCCESS)
        ]),
        Sequence([
            Condition(lambda bb: bb.get("enemy_visible")),
            Action(lambda bb: print("攻击!") or Status.SUCCESS)
        ]),
        Action(lambda bb: print("巡逻...") or Status.SUCCESS)
    ]),
    blackboard=bb
)

# 执行
tree.tick()  # 输出: 攻击!
```

---

## 实战要点

### 1. 行为树层级控制

- **主动 AI**（玩家附近）：完整树，10-30Hz tick
- **被动 AI**（远距离）：简化树（只保留核心节点），1-5Hz tick
- 使用 LOD（细节层次）技术在不同距离切换行为树的复杂度

### 2. 行为树 + 实时脚本（混合方案）

提出了一种行为树/规划器混合方案：
- **上层**：行为树控制流程和大阶段
- **底层**：规划器动态生成动作序列
- 适合复杂任务（如"在掩体间移动并压制敌人"）

### 3. 调试技巧

- **可视化**：实时高亮当前活动节点
- **日志**：记录每个节点的 tick 次数、成功/失败比
- **断点**：在特定节点设置条件断点，检查黑板状态

### 4. 性能优化

- **Running 节点缓存**：避免每次都从根节点全量遍历
- **节点池化**：复用动作节点减少GC压力
- **条件排序**：Selector 中最可能成功的条件放最左

---

## 反模式（NEVER 列表）

| # | 反模式 | 原因 |
|---|--------|------|
| 1 | ❌ 嵌套超过 10 层 | 难以调试和维护，超过6层就应该考虑拆分为子树 |
| 2 | ❌ 条件节点中执行副作用 | Condition 应只检查状态，不应修改状态 |
| 3 | ❌ 动作节点跨帧持有隐式状态 | 应使用 Running 状态让树来控制执行周期 |
| 4 | ❌ 全局黑板共享所有数据 | 应分层隔离黑板作用域 |
| 5 | ❌ 每帧重建行为树 | 结构应缓存，运行时可调整参数而非结构 |
| 6 | ❌ 行为树内写复杂算法 | 复杂计算应封装在独立的服务模块中 |

---

## See Also

- [[效用理论|效用理论]] ([效用理论](效用理论.md)) — 当行为树的选择逻辑变得复杂时，Utility 系统提供更灵活的评分选择
- [[HTN层次任务规划|HTN层次任务规划]] ([HTN层次任务规划](HTN层次任务规划.md)) — 需要自动规划多步骤任务时，HTN 比行为树更适合
- [[游戏AI架构|游戏AI架构]] ([游戏AI架构](游戏AI架构.md)) — 行为树在整体AI架构中的定位
- [[游戏AI架构体系|游戏AI架构体系]] ([游戏AI架构体系](游戏AI架构体系.md)) — 系统学习AI架构完整体系，行为树在整体架构中的定位

---
