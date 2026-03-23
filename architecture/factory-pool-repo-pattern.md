# Factory + Pool + Repo 对象管理模式

**适用场景**: prototype, production, refactor

## 核心思想

对象生命周期管理分三层：
- Factory：创建与初始化
- Pool：复用与回收
- Repo：注册与查询

## Factory 职责

### 应该做什么

- 封装对象创建逻辑
- 注入依赖
- 初始化状态
- 注册到 Repo

### 示例

```csharp
public class BattleFactory {
    MonsterPool monsterPool;
    IDService idService;

    public MonsterEntity Monster_Create(
        BattleSystemContext ctx,
        MonsterConfigTM config,
        Vector3 position) {

        // 从池获取
        MonsterEntity entity = monsterPool.Get(position);

        // 初始化
        int id = idService.NextMonsterId();
        entity.Init(id, config);

        // 注入上下文
        entity.InjectCtx(ctx);

        // 注册
        ctx.monsterRepo.Register(entity);

        return entity;
    }
}
```

## Pool 职责

### 应该做什么

- 管理对象复用
- 提供 Get/Return 接口
- 处理池扩容

### 不应该做什么

- 不负责业务初始化
- 不负责注册到 Repo

### 示例

```csharp
public class MonsterPool {
    Queue<MonsterEntity> normalPool;
    Queue<MonsterEntity> explodePool;
    Queue<MonsterEntity> bossPool;

    public MonsterEntity GetNormal(Vector3 pos) {
        if (normalPool.Count == 0) {
            // 扩容
        }
        var entity = normalPool.Dequeue();
        entity.transform.position = pos;
        entity.gameObject.SetActive(true);
        return entity;
    }

    public void Return(MonsterEntity entity) {
        entity.gameObject.SetActive(false);
        if (entity.isBoss) {
            bossPool.Enqueue(entity);
        } else if (entity.explodesOnDeath) {
            explodePool.Enqueue(entity);
        } else {
            normalPool.Enqueue(entity);
        }
    }
}
```

## Repo 职责

### 应该做什么

- 维护活动对象注册表
- 提供查询接口
- 提供遍历接口

### 不应该做什么

- 不负责创建对象
- 不负责回收对象

### 示例

```csharp
public class MonsterRepo {
    Dictionary<int, MonsterEntity> dict;

    public void Register(MonsterEntity entity) {
        dict[entity.ID] = entity;
    }

    public void Unregister(int id) {
        dict.Remove(id);
    }

    public MonsterEntity Get(int id) {
        return dict.TryGetValue(id, out var entity) ? entity : null;
    }

    public void Foreach(Action<MonsterEntity> action) {
        foreach (var entity in dict.Values) {
            action(entity);
        }
    }

    public void Clear() {
        dict.Clear();
    }
}
```

## 完整流程

### 创建
```
Domain 调用 Factory.Create()
  → Factory 从 Pool.Get()
  → Factory 初始化
  → Factory 注册到 Repo
  → 返回实体
```

### 销毁
```
Domain 调用 Factory.Destroy()
  → Factory 从 Repo.Unregister()
  → Factory 调用 Pool.Return()
```

## 与其他模式的关系

- 配合"system-domain-pattern"使用
- 配合"composition-root-pattern"使用
