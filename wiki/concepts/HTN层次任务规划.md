---
title: HTN层次任务规划
category: concept
tags: [游戏AI, HTN, 层次规划, 任务网络, 自动规划]
related: []
sources: [Game AI Pro Ch.12 HTN规划器, Game AI Pro Ch.13 多单位分层规划, Game AI Pro 2 Ch.13 实用规划优化]
confidence: high
synthesized: 2026-06-23
summary: HTN层次任务网络规划器：任务分解、方法选择、世界状态建模，多单位战斗分层规划空间搜索。
---


# HTN 规划器（Hierarchical Task Network Planner）


## 概述

HTN（层次任务网络）规划器是一种基于任务分解的 AI 规划技术。与行为树的"预编排"不同，HTN 在运行时**动态生成**行为序列。

核心思想：

> **顶层任务不断分解为更具体的子任务，直到得到可直接执行的原语动作序列。**

分别介绍了 HTN 的基础原理和在多单位战斗中的高级应用。

### HTN vs 其他方案

| 维度 | 行为树 | HTN | GOAP |
|------|--------|-----|------|
| 行为定义 | 手动编排 | 任务+方法 | 动作+效果 |
| 运行时行为 | 固定结构 | 动态生成 | 搜索生成 |
| 可预测性 | 高 | 中 | 低 |
| 灵活性 | 低 | 中 | 高 |
| 实现复杂度 | 低 | 中 | 高 |
| 典型场景 | 标准AI | 战术规划 | 复杂策略 |

---

## 核心概念

### HTN 的核心元素

```
HTN 世界
├── 任务（Task）
│   ├── 原语任务（Primitive Task） — 可直接执行的动作
│   └── 复合任务（Compound Task） — 需要分解的任务
│
├── 方法（Method）
│   └── 定义一个复合任务如何分解为子任务
│       可以有多条"方法"（不同分解路径）
│
├── 世界状态（World State）
│   └── AI 已知的当前世界状况（知识表示的简化版）
│
└── 规划器（Planner）
    └── 负责将复合任务不断分解，直到全部变成原语任务
```

### 规划过程

```
输入:   复合任务 "消灭敌人"
        世界状态 {弹药:30, 血量:80, 位置:A1}

步骤1: 查找 "消灭敌人" 的方法
       方法A: 如果有武器 → 分解为 [接近目标, 攻击目标]
       方法B: 如果没武器 → 分解为 [寻找武器, 消灭敌人]

步骤2: 根据世界状态选择方法A
       "消灭敌人" → [接近目标, 攻击目标]

步骤3: 分解 "接近目标"
       方法: [移动到A2, 移动到A3]
       → [移动到A2, 移动到A3, 攻击目标]

步骤4: 分解 "攻击目标"
       方法: 如果有弹药 → [瞄准, 开火]
       → [移动到A2, 移动到A3, 瞄准, 开火]

输出:   [移动到A2, 移动到A3, 瞄准, 开火]
       （全部是原语任务，可直接执行）
```

---

## 伪代码实现

```python
from enum import Enum
from typing import List, Callable

class TaskType(Enum):
    PRIMITIVE = 1   # 原语任务（可直接执行）
    COMPOUND = 2    # 复合任务（需要分解）

class Task:
    """任务基类"""
    def __init__(self, name: str, task_type: TaskType):
        self.name = name
        self.type = task_type

class PrimitiveTask(Task):
    """原语任务：可直接执行的动作"""
    def __init__(self, name: str, execute_fn: Callable):
        super().__init__(name, TaskType.PRIMITIVE)
        self.execute_fn = execute_fn

    def execute(self, state):
        self.execute_fn(state)

class CompoundTask(Task):
    """复合任务：需要分解为子任务"""
    def __init__(self, name: str):
        super().__init__(name, TaskType.COMPOUND)
        self.methods = []  # 多条分解方法

    def add_method(self, method):
        self.methods.append(method)

class Method:
    """分解方法：定义如何将复合任务分解为子任务序列"""
    def __init__(self, name: str, subtasks: List[Task],
                 condition: Callable = None):
        self.name = name
        self.subtasks = subtasks
        self.condition = condition  # 适用条件

    def is_valid(self, state) -> bool:
        """检查此方法在当前状态下是否可用"""
        return self.condition(state) if self.condition else True


class HTNPlanner:
    """HTN 规划器"""
    def __init__(self, domain_knowledge: dict):
        self.tasks = domain_knowledge.get("tasks", {})
        self.state = {}

    def plan(self, task_name: str, state: dict) -> List[Task]:
        """规划：将任务分解为原语动作序列"""
        self.state = dict(state)  # 复制状态
        plan_steps = []

        def decompose(task):
            if task.type == TaskType.PRIMITIVE:
                plan_steps.append(task)
                return True

            # 尝试每条方法
            for method in task.methods:
                if not method.is_valid(self.state):
                    continue

                # 递归分解所有子任务
                all_success = True
                for subtask in method.subtasks:
                    if not decompose(subtask):
                        all_success = False
                        break

                if all_success:
                    return True

            # 没有可用方法 → 规划失败
            return False

        root = self.tasks.get(task_name)
        if not root:
            return []

        if decompose(root):
            return plan_steps
        return []


# ---- 使用示例 ----

# 定义原语任务
def move_to(target):
    def _execute(state):
        print(f"移动到 {target}")
        state["position"] = target
    return PrimitiveTask(f"移动到{target}", _execute)

def attack():
    def _execute(state):
        print(f"攻击！弹药={state.get('ammo', 0)}")
        state["ammo"] = max(0, state.get("ammo", 0) - 1)
    return PrimitiveTask("攻击", _execute)

def reload():
    def _execute(state):
        print("换弹")
        state["ammo"] = 30
    return PrimitiveTask("换弹", _execute)

# 定义复合任务
fight = CompoundTask("战斗")

# 方法1: 有弹药 → 攻击
fight.add_method(Method(
    "有弹药就攻击",
    subtasks=[move_to("敌人位置"), attack()],
    condition=lambda s: s.get("ammo", 0) > 0
))

# 方法2: 没弹药 → 换弹后再打
fight.add_method(Method(
    "换弹后攻击",
    subtasks=[reload(), move_to("敌人位置"), attack()],
    condition=lambda s: s.get("ammo", 0) == 0
))

# 规划器
planner = HTNPlanner({"tasks": {"战斗": fight}})
plan = planner.plan("战斗", {"position": "A1", "ammo": 0})

print("=== 规划结果 ===")
for task in plan:
    task.execute(planner.state)
# 输出:
# 换弹
# 移动到 敌人位置
# 攻击！弹药=29
```

---

## 实战要点

### 多单位战斗的分层规划

将 HTN 扩展到多单位场景，引入了**分层计划空间搜索**：

```
层2: 指挥官级
    任务: "占领区域B"
    方法: 步兵压制 + 突击队侧翼

层1: 小队级
    任务: "压制区域B"
    方法: [移动到掩体, 持续射击]

层0: 个体级
    任务: "移动到掩体"
    方法: [寻路到位置, 跑过去]
```

**关键设计：**
- 每一层只关注自己的职责范围
- 上层给下层设定目标，下层返回执行计划
- 失败时向上层报告，上层重新规划

### 规划失败处理

HTN 的一个关键挑战是规划可能失败：

```python
def execute_with_fallback(planner, task_name, state):
    """执行规划，并在失败时降级"""
    plan = planner.plan(task_name, state)
    if not plan:
        # 规划失败 → 降级到默认行为
        print("无法规划，使用默认行为")
        return default_behavior(state)

    # 执行规划
    for i, task in enumerate(plan):
        if not task.execute(state):
            # 执行失败 → 重新规划剩余任务
            print(f"任务 {task.name} 失败，重新规划")
            remaining = planner.plan(task_name, state)
            if remaining:
                return execute_with_fallback(planner, task_name, state)
            break
```

---

## 反模式（NEVER 列表）

| # | 反模式 | 原因 |
|---|--------|------|
| 1 | ❌ 每帧都重新规划 | 规划有计算开销，应只在状态变化时重规划 |
| 2 | ❌ 无限递归的任务分解 | 必须有终止条件（原语任务） |
| 3 | ❌ 方法条件过于复杂 | 方法条件应简洁，复杂逻辑放在世界状态中 |
| 4 | ❌ 忽略规划失败处理 | 必须提供 fallback 方案 |
| 5 | ❌ 过度使用 HTN | 简单 AI 不需要规划器，行为树就够了 |

---

## See Also

- [[游戏AI行为树|游戏AI行为树]] ([游戏AI行为树](游戏AI行为树.md)) — HTN 的替代方案，更简单但灵活度低
- [[游戏AI架构|游戏AI架构]] ([游戏AI架构](游戏AI架构.md)) — 规划器在整体AI架构中的定位
- [[战术推理|战术推理]] ([战术推理](战术推理.md)) — HTN 可用于实现高级战术规划
- [[游戏AI架构体系|游戏AI架构体系]] ([游戏AI架构体系](游戏AI架构体系.md)) — 系统学习AI架构完整体系，HTN在架构中的定位与更多案例

---
