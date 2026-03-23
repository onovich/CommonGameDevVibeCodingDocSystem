# 事件驱动的跨系统通信

**适用场景**: production

## 核心思想

System 之间不直接调用，通过事件通信。

## 为什么需要事件

### 问题场景

System A 直接调用 System B：
```csharp
// StorySystem 里
playSystem.StartToy(toyId);
```

问题：
- StorySystem 依赖 PlaySystem
- 难以测试
- 难以扩展

### 解决方案

通过事件通信：
```csharp
// StorySystem 里
evt.Story_OnRequestStartToy?.Invoke(toyId);

// Main.cs 里绑定
storySystem.Evt.Story_OnRequestStartToy += playSystem.StartToy;
```

## Events 类结构

每个 System / App 有一个 Events 类：

```csharp
public class StorySystemEvents {
    // Handler 字段
    public Action<int> Story_OnRequestStartToyHandler;

    // 触发方法
    public void Story_OnRequestStartToy(int toyId) {
        Story_OnRequestStartToyHandler?.Invoke(toyId);
    }

    public void Clear() {
        Story_OnRequestStartToyHandler = null;
    }
}
```

## 命名规范

### Handler 字段
- `来源_动词_名词Handler`
- 例如：`Story_OnRequestStartToyHandler`

### 触发方法
- 与 Handler 同前缀，去掉 `Handler` 后缀
- 例如：`Story_OnRequestStartToy`

## 绑定位置

所有跨系统事件绑定只在 Main.cs 中：

```csharp
void BindEvents() {
    storySystem.Evt.Story_OnRequestStartToy += playSystem.StartToy;
    playSystem.Evt.Play_OnToyComplete += storySystem.OnToyComplete;
}
```

## 与其他模式的关系

- 配合"system-domain-pattern"使用
- 配合"composition-root-pattern"使用
