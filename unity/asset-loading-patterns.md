# Unity 资源加载模式与常见坑

**适用场景**: prototype, production, refactor

## Resources.Load 模式

### 基本用法

```csharp
var config = Resources.Load<MonsterConfigSO>("Configs/NormalMonster");
```

### 路径规则

- 相对于 `Assets/Resources/` 目录
- 不包含文件扩展名
- 大小写敏感

## 定向加载 vs 宽泛扫描

### 定向加载（推荐）

```csharp
public void Init() {
    CacheMonsterConfig("NormalMonster");
    CacheMonsterConfig("ExplodeMonster");
    CacheMonsterConfig("BossMonster");
}

void CacheMonsterConfig(string name) {
    var so = Resources.Load<MonsterConfigSO>($"Configs/{name}");
    monsterConfigDict[name] = so;
}
```

优势：
- 明确知道加载了什么
- 不会触发 legacy 资产问题
- 性能可控

### 宽泛扫描（慎用）

```csharp
var all = Resources.LoadAll<MonsterConfigSO>("Configs/");
```

问题：
- 可能加载到 legacy 资产
- 可能触发 missing script warnings
- 性能不可控

## 常见坑

### 坑 1: 混有 legacy 资产的目录

**场景**：
- `Assets/Resources/Configs` 混有旧资产
- 旧资产引用了已删除的脚本
- `LoadAll` 会触发 repeated missing-script warnings

**解决方案**：
- 使用定向加载
- 或分离 legacy 和 current 资产

### 坑 2: 路径大小写

**场景**：
- Windows 不区分大小写
- 其他平台区分大小写
- 可能导致跨平台问题

**解决方案**：
- 统一使用小写或 PascalCase
- 在 Windows 上也严格匹配大小写

### 坑 3: 忘记文件扩展名

**错误**：
```csharp
Resources.Load<MonsterConfigSO>("Configs/NormalMonster.asset");
```

**正确**：
```csharp
Resources.Load<MonsterConfigSO>("Configs/NormalMonster");
```

## 与其他模式的关系

- 配合"config-driven-runtime"使用
- 是"prototype"和"production"阶段都需要注意的问题
