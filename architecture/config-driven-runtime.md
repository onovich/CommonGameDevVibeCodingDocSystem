# 配置驱动运行时设计

**适用场景**: prototype, production

## 核心思想

所有数值、规则、资源引用放在配置对象里，运行时只读。

## 为什么配置驱动

### 优势

- 数值调整不需要改代码
- 策划可以直接修改配置
- 易于测试不同配置
- 易于版本管理

### 对比硬编码

| 维度 | 配置驱动 | 硬编码 |
|------|---------|--------|
| 修改成本 | 低 | 高 |
| 测试成本 | 低 | 高 |
| 策划友好 | 是 | 否 |
| 版本管理 | 易 | 难 |

## 配置对象类型

### ScriptableObject (Unity)

```csharp
[CreateAssetMenu]
public class MonsterConfigSO : ScriptableObject {
    public string monsterName;
    public float maxHP;
    public float moveSpeed;
    public bool explodesOnDeath;
    public bool isBoss;
}
```

### TM (传输模型)

```csharp
[Serializable]
public struct MonsterConfigTM {
    public string monsterName;
    public float maxHP;
    public float moveSpeed;
    public bool explodesOnDeath;
    public bool isBoss;
}
```

### SO vs TM

- SO：Unity 资产，Inspector 可编辑
- TM：纯数据结构，可序列化

推荐：SO 作为编辑器资产，TM 作为运行时数据。

## 配置加载模式

### 定向加载

```csharp
public class AssetsInfra {
    Dictionary<string, MonsterConfigSO> monsterConfigDict;

    public void Init() {
        CacheMonsterConfig("NormalMonster");
        CacheMonsterConfig("ExplodeMonster");
        CacheMonsterConfig("BossMonster");
    }

    void CacheMonsterConfig(string name) {
        var so = Resources.Load<MonsterConfigSO>($"Configs/{name}");
        monsterConfigDict[name] = so;
    }
}
```

### 避免宽泛扫描

不要：
```csharp
// 宽泛扫描，可能触发 legacy 资产问题
var all = Resources.LoadAll<MonsterConfigSO>("Configs/");
```

应该：
```csharp
// 定向加载已知资产
CacheMonsterConfig("NormalMonster");
CacheMonsterConfig("ExplodeMonster");
```

## 配置使用模式

### 运行时只读

```csharp
public class MonsterEntity {
    MonsterConfigTM config;

    public void Init(int id, MonsterConfigTM cfg) {
        this.config = cfg;
        // 使用配置初始化
        maxHP = cfg.maxHP;
        moveSpeed = cfg.moveSpeed;
    }
}
```

### 不要运行时修改配置

不要：
```csharp
config.maxHP *= 1.5f; // 修改配置对象
```

应该：
```csharp
float actualHP = config.maxHP * 1.5f; // 使用配置计算
```

## 与其他模式的关系

- 是"prototype"和"production"阶段都应该遵守的原则
- 配合"factory-pool-repo-pattern"使用
