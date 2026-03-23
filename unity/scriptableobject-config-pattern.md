# Unity ScriptableObject 配置驱动模式

**适用场景**: prototype, production

## 核心思想

使用 ScriptableObject 作为配置资产，运行时只读。

## 创建 ScriptableObject

```csharp
[CreateAssetMenu(fileName = "MonsterConfig", menuName = "Configs/Monster")]
public class MonsterConfigSO : ScriptableObject {
    public string monsterName;
    public float maxHP;
    public float moveSpeed;
    public bool explodesOnDeath;
    public bool isBoss;
}
```

## 程序化创建

```csharp
public static class ConfigBuilder {
    public static void CreateMonsterConfig(string name, float hp, float speed) {
        var config = ScriptableObject.CreateInstance<MonsterConfigSO>();
        config.monsterName = name;
        config.maxHP = hp;
        config.moveSpeed = speed;

        string path = $"Assets/Resources/Configs/{name}.asset";
        AssetDatabase.CreateAsset(config, path);
    }
}
```

## SO vs TM

### ScriptableObject (SO)
- Unity 资产
- Inspector 可编辑
- 可被 Prefab 引用

### 传输模型 (TM)
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

- 纯数据结构
- 可序列化
- 运行时使用

### 推荐模式

```csharp
public class MonsterConfigSO : ScriptableObject {
    public MonsterConfigTM tm; // SO 包含 TM
}

// 运行时
entity.Init(config.tm); // 传递 TM
```

## 与其他模式的关系

- 配合"config-driven-runtime"使用
- 配合"asset-loading-patterns"使用
