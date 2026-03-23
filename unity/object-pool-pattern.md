# Unity 对象池模式

**适用场景**: prototype, production

## 为什么需要对象池

### 问题场景

频繁创建/销毁对象：
- 性能开销大
- 产生 GC 压力
- 可能导致卡顿

### 解决方案

对象池复用对象：
- 减少创建/销毁开销
- 减少 GC 压力
- 性能稳定

## 基本实现

```csharp
public class MonsterPool {
    Queue<MonsterEntity> pool;
    GameObject prefab;
    Transform poolRoot;

    public MonsterEntity Get(Vector3 position) {
        MonsterEntity entity;
        if (pool.Count > 0) {
            entity = pool.Dequeue();
        } else {
            var go = GameObject.Instantiate(prefab, poolRoot);
            entity = go.GetComponent<MonsterEntity>();
        }
        entity.transform.position = position;
        entity.gameObject.SetActive(true);
        return entity;
    }

    public void Return(MonsterEntity entity) {
        entity.gameObject.SetActive(false);
        pool.Enqueue(entity);
    }
}
```

## 多类型池

```csharp
public class MonsterPool {
    Queue<MonsterEntity> normalPool;
    Queue<MonsterEntity> explodePool;
    Queue<MonsterEntity> bossPool;

    public MonsterEntity GetNormal(Vector3 pos) { ... }
    public MonsterEntity GetExplode(Vector3 pos) { ... }
    public MonsterEntity GetBoss(Vector3 pos) { ... }

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

## 池路由

### 问题

调用方需要知道该还到哪个池。

### 解决方案

集中路由逻辑：

```csharp
public class BattleFactory {
    MonsterPool monsterPool;

    public void Monster_Destroy(MonsterEntity entity) {
        ctx.monsterRepo.Unregister(entity.ID);
        monsterPool.Return(entity); // 池内部判断类型
    }
}
```

## 与其他模式的关系

- 配合"factory-pool-repo-pattern"使用
- 是"prototype"和"production"阶段都应该遵守的模式
