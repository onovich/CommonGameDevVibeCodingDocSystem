# Unity MonoBehaviour 生命周期常见问题

**适用场景**: prototype, production, refactor

## OnEnable 时机早于 Init

### 问题场景

```csharp
// 对象池 Get
entity.gameObject.SetActive(true); // 触发 OnEnable
entity.Init(config); // Init 在 OnEnable 之后
```

OnEnable 时 entity 数据还是空的。

### 解决方案

不要在 OnEnable 里读取 entity 数据：

```csharp
// 错误
void OnEnable() {
    UpdateUI(entity.maxHP); // entity 还没 Init
}

// 正确
public void Show(string name, float hp) {
    // 显式调用，在 Init 之后
    UpdateUI(name, hp);
}
```

## Physics + Transform 冲突

### 问题场景

```csharp
void Update() {
    transform.Translate(velocity * Time.deltaTime);
}
```

有 Rigidbody2D 时会冲突。

### 解决方案

```csharp
void FixedUpdate() {
    rb.MovePosition(rb.position + velocity * Time.fixedDeltaTime);
}
```

## CameraFollow 抖动

### 问题场景

```csharp
void LateUpdate() {
    transform.position = Vector3.Lerp(
        transform.position,
        target.position,
        Time.deltaTime * speed);
}
```

物理在 FixedUpdate，相机在 LateUpdate，频率不同导致抖动。

### 解决方案

```csharp
void FixedUpdate() {
    transform.position = target.position; // 硬跟随
}
```

## EventSystem 缺失

### 问题场景

Button 的 onClick 已绑定，但点击没反应。

### 原因

Unity UI 的所有输入事件都依赖 EventSystem。

### 解决方案

```csharp
var esGo = new GameObject("EventSystem");
esGo.AddComponent<EventSystem>();
esGo.AddComponent<StandaloneInputModule>();
```

## 与其他文档的关系

- 是"prototype"和"production"阶段都需要注意的问题
- 配合"batchmode-verification"使用
