# 组合根与依赖注入

**适用场景**: production, refactor

## 核心思想

在一个地方完成所有对象的创建、装配、初始化。

## 为什么需要组合根

### 问题场景

依赖关系散落各处：
- 对象在多处创建
- 依赖关系不清晰
- 初始化顺序混乱
- 难以测试

### 解决方案

集中到组合根：
- 显式创建所有对象
- 显式注入依赖
- 显式控制初始化顺序

## 组合根职责

### 应该做什么

- 创建所有根对象
- 注入依赖
- 控制初始化顺序
- 启动系统

### 不应该做什么

- 不写业务规则
- 不持有业务状态

## 示例

```csharp
public class Main : MonoBehaviour {
    void Start() {
        // 1. 创建基础设施
        var assetsInfra = new AssetsInfra();
        var rdService = new RandomService();
        var idService = new IDService();

        // 2. 创建应用层
        var uiApp = new UIApp();
        var battleSystem = new BattleSystem();

        // 3. 注入依赖
        assetsInfra.Inject(gameConfig);
        uiApp.Inject(uiPanelRoot, uiHUDRoot, mainCamera, assetsInfra);
        battleSystem.Inject(
            assetsInfra, rdService, idService,
            poolRoot, roleEntity, mainCamera,
            gameConfig.tm, uiApp);

        // 4. 初始化
        assetsInfra.Init();
        battleSystem.Init(
            roleBulletPrefab,
            explodeBulletPrefab,
            normalMonsterPrefab,
            explodeMonsterPrefab,
            bossMonsterPrefab);

        // 5. 启动 UI
        UIRoleDomain.Open(uiApp.Ctx);
        UIWaveInfoDomain.Open(uiApp.Ctx);

        // 6. 启动系统
        battleSystem.Enter();
    }

    void Update() {
        battleSystem?.Tick(Time.deltaTime);
    }

    void OnDestroy() {
        battleSystem?.TearDown();
        uiApp?.TearDown();
    }
}
```

## 依赖注入原则

### 构造函数注入 vs Inject 方法

Unity MonoBehaviour 不支持构造函数注入，使用 Inject 方法：

```csharp
public class BattleSystem {
    public void Inject(
        AssetsInfra assets,
        RandomService random,
        IDService ids,
        UIApp uiApp) {
        // 注入依赖
    }
}
```

### 分离 Inject 和 Init

- Inject：注入依赖
- Init：初始化状态

```csharp
// 先注入
battleSystem.Inject(assets, random, ids, uiApp);

// 再初始化
battleSystem.Init(prefab1, prefab2, prefab3);
```

## 生命周期对称性

```csharp
// 启动
Inject → Init → Enter

// 运行
Tick

// 停止
Exit → TearDown
```

## 与其他模式的关系

- 配合"system-domain-pattern"使用
- 配合"factory-pool-repo-pattern"使用
- 配合"ui-boundary-pattern"使用
