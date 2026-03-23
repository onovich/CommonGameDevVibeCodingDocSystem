# System + Domain 分层模式

**适用场景**: production, refactor

## 核心思想

- System 负责驱动和装配
- Domain 负责业务规则

## System 层职责

### 应该做什么

- 帧驱动：统一的 Tick/Update 入口
- 装配：Inject/Init/Enter/Exit/TearDown
- 生命周期管理：启动、暂停、恢复、销毁
- 调度：按顺序调用各 Domain

### 不应该做什么

- 不写具体业务规则
- 不直接操作实体字段
- 不成为上帝类

### 示例

```csharp
public class BattleSystem {
    BattleSystemContext Ctx;

    public void Inject(...) {
        Ctx = new BattleSystemContext();
        // 装配依赖
    }

    public void Init(...) {
        // 初始化
    }

    public void Enter() {
        // 进入
    }

    public void Tick(float dt) {
        WaveDomain.Tick(Ctx, dt);
        MonsterDomain.Tick(Ctx, dt);
        RoleDomain.Tick(Ctx, dt);
    }

    public void Exit() {
        // 退出
    }

    public void TearDown() {
        Ctx?.Clear();
        Ctx = null;
    }
}
```

## Domain 层职责

### 应该做什么

- 承载跨对象业务规则
- 以 Context 为主要输入
- 保持无状态（静态方法）

### 不应该做什么

- 不持有状态
- 不直接依赖具体 UI 组件
- 不直接依赖具体资源路径

### 示例

```csharp
public static class WaveDomain {
    public static void Tick(BattleSystemContext ctx, float dt) {
        if (ctx.waveEntity.phase == WavePhase.Idle) {
            Idle_Tick(ctx, dt);
        } else if (ctx.waveEntity.phase == WavePhase.Spawning) {
            Spawning_Tick(ctx, dt);
        }
    }

    static void Idle_Tick(BattleSystemContext ctx, float dt) {
        // 业务规则
    }
}
```

## Context 的作用

Context 是 System 和 Domain 之间的共享上下文：

```csharp
public class BattleSystemContext {
    // 配置
    public GameConfigTM gameTm;

    // 基础设施
    public AssetsInfra assets;
    public UIApp uiApp;

    // 实体
    public RoleEntity roleEntity;
    public WaveEntity waveEntity;

    // 仓库
    public MonsterRepo monsterRepo;
    public BulletRepo bulletRepo;

    // 工厂
    public BattleFactory factory;

    public void Clear() {
        monsterRepo?.Clear();
        bulletRepo?.Clear();
    }
}
```

## 与其他模式的关系

- 配合"composition-root-pattern"使用
- 配合"factory-pool-repo-pattern"使用
- 配合"ui-boundary-pattern"使用
