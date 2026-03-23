# Survivor-like 游戏架构模式

**适用场景**: prototype, production

## 核心系统

### 1. 波次系统 (Wave System)

**职责**：
- 管理波次推进
- 控制敌人生成节奏
- 判定波次完成

**关键状态**：
```
- 当前波次编号
- 波次阶段 (Idle / Spawning / WaitingClear)
- 活动敌人计数
```

**典型流程**：
```
Idle → 读取波次配置 → Spawning → 生成敌人 → WaitingClear → 等待清空 → Idle
```

### 2. 怪物系统 (Monster System)

**职责**：
- 怪物生成
- 怪物 AI
- 怪物死亡处理

**典型类型**：
- 普通怪：向玩家移动
- 爆炸怪：死亡时发射子弹
- Boss：高 HP + 周期性召唤小怪
- 假 Boss：吸引小怪但不攻击

### 3. 角色系统 (Role System)

**职责**：
- 玩家移动
- 自动瞄准
- 自动射击

**典型实现**：
- WASD 移动
- 朝最近敌人射击
- HP 管理

### 4. 拾取物系统 (Pickup System)

**职责**：
- 拾取物生成
- 拾取检测
- 拾取效果

**典型类型**：
- 弹药包
- 血包
- 经验球

## 推荐架构

```
Main (组合根)
  ↓
BattleSystem (帧驱动)
  ↓
WaveDomain (波次推进)
MonsterDomain (怪物 AI)
RoleDomain (角色战斗)
PickupDomain (拾取物)
  ↓
BattleFactory (创建)
MonsterRepo / BulletRepo (注册)
MonsterPool / BulletPool (复用)
  ↓
UIApp (UI 边界)
```

## 配置驱动

### 怪物配置
```
- 名称
- HP
- 移动速度
- 是否爆炸
- 是否 Boss
```

### 波次配置
```
- 波次编号
- 普通怪数量
- 爆炸怪数量
- Boss 数量
- 假 Boss 数量
```

## 与其他文档的关系

- 配合"system-domain-pattern"使用
- 配合"factory-pool-repo-pattern"使用
- 配合"config-driven-runtime"使用
