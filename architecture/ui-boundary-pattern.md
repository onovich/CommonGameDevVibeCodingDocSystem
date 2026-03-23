# UI 边界与逻辑层解耦

**适用场景**: production, refactor

## 核心思想

逻辑层可以调用 UI，但不直接操作具体 UI 组件。

## 为什么需要 UI 边界

### 问题场景

逻辑层直接操作 UI 组件：

```csharp
// 业务逻辑里
bossHealthBar.slider.value = hp / maxHp;
waveInfoText.text = $"Wave {wave}";
```

问题：
- 逻辑层依赖具体 UI 实现
- UI 改动会影响逻辑层
- 难以测试

### 解决方案

引入 UI 边界层：

```csharp
// 业务逻辑里
uiApp.BossBar_UpdateHP(hp, maxHp);
uiApp.WaveInfo_UpdateWave(wave);
```

## UI 边界层职责

### 应该做什么

- 提供逻辑层调用的 API
- 封装具体 UI 组件操作
- 管理 UI 生命周期

### 不应该做什么

- 不写业务规则
- 不直接依赖业务实体

### 示例

```csharp
public class UIApp {
    UIAppContext Ctx;

    public void BossBar_Show(string name, float hp) {
        UIBossBarDomain.Show(Ctx, name, hp);
    }

    public void BossBar_UpdateHP(float current, float max) {
        UIBossBarDomain.UpdateHP(Ctx, current, max);
    }

    public void BossBar_Hide() {
        UIBossBarDomain.Hide(Ctx);
    }

    public void WaveInfo_UpdateWave(int wave) {
        UIWaveInfoDomain.UpdateWave(Ctx, wave);
    }
}
```

## UI Domain 的作用

UI Domain 负责具体 UI 组件操作：

```csharp
public static class UIBossBarDomain {
    public static void Show(UIAppContext ctx, string name, float hp) {
        var panel = UIFactory.BossBarPanel_Open(ctx);
        panel.nameText.text = name;
        panel.hpSlider.value = 1f;
    }

    public static void UpdateHP(UIAppContext ctx, float current, float max) {
        var panel = UIFactory.BossBarPanel_Get(ctx);
        if (panel == null) return;
        panel.hpSlider.value = current / max;
    }

    public static void Hide(UIAppContext ctx) {
        UIFactory.BossBarPanel_Close(ctx);
    }
}
```

## 调用路径

```
逻辑层 (Domain)
  ↓ 调用
UI 边界 (UIApp)
  ↓ 调用
UI Domain (UIBossBarDomain)
  ↓ 操作
具体 UI 组件 (Panel/HUD)
```

## 不要重新引入事件转发

### 错误做法

```csharp
// 逻辑层
events.OnBossHPChanged?.Invoke(hp, maxHp);

// Main 里
events.OnBossHPChanged += uiApp.BossBar_UpdateHP;
```

这样只是多绕了一层，没有解耦。

### 正确做法

```csharp
// 逻辑层直接调用 UI 边界
ctx.uiApp.BossBar_UpdateHP(hp, maxHp);
```

## 与其他模式的关系

- 配合"system-domain-pattern"使用
- 配合"composition-root-pattern"使用
