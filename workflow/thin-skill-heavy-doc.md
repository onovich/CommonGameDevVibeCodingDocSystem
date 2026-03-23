# 薄 Skill + 厚文档模式

**适用场景**: prototype, production, refactor

## 核心原则

Skill 负责调度，文档负责知识。

## 为什么分离

### 问题场景
- skill 里塞大段知识，导致 skill 本身成为高 token 入口
- 文档里没有调度逻辑，AI 不知道该读什么
- 知识更新时需要同时更新 skill 和文档

### 分离的优势
- skill 保持轻量，只负责流程控制
- 文档沉淀长期知识，易于维护
- 知识更新时只需更新文档
- AI 按需读取，降低 token 消耗

## Skill 应该写什么

### 调度逻辑
```markdown
1. 先读：docs/xxx.md
2. 再按需读：docs/yyy.md, docs/zzz.md
3. 只读取当前 slice 直接相关的代码
```

### 边界控制
```markdown
- 一次只允许推进一个 slice
- 不要一上来读取整个 docs/
- 不要把本 skill 用成"整仓重构"入口
```

### 输出格式
```markdown
输出保持简短，只报告当前 slice：

Scope
- ...

Invariants
- ...
```

### 验证节奏
```markdown
- 验证时优先复用 /verify-xxx
- 如果新增了文件，必须遵守两步编译规则
```

## Skill 不应该写什么

### 不要写长期知识
- 完整架构定义
- 详细代码示例
- 长篇方法论
- 反模式映射表

这些应该放在文档里。

### 不要重复文档内容

如果文档已经写了，skill 只需引用：

```markdown
先读：docs/refactor-playbook.md
```

不要在 skill 里再复制一遍。

## 文档应该写什么

### 长期知识
- 架构定义
- 代码锚点
- 反模式映射
- 验证流程
- 常见坑

### 详细说明
- 为什么这样做
- 不要做什么
- 常见弯路
- 完成判定

## 示例

### 薄 skill
```markdown
---
name: unity-refactor-playbook
description: Guide one low-churn Unity refactor slice
---

## Behavior

1. 先读：docs/unity-refactor-playbook.md
2. 再按需读：docs/unity-arch.md, docs/unity-workflow.md
3. 只读取当前 slice 直接相关的代码
4. 输出固定格式：Scope / Invariants / ...
5. 一次只允许推进一个 slice
6. 验证时优先复用 /verify-unity

## Guardrails

- 不要一上来读取整个 docs/
- 不要把本 skill 用成"整仓重构"入口
```

### 厚文档
```markdown
# Unity 原型项目低开销收敛手册

## 目的与适用范围
...

## 低 churn 改造顺序
...

## 反模式 → 目标结构映射
...

## 代码锚点
...
```

## 与其他方法的关系

- 是"AI 低 token 工作流"的核心模式
- 配合"文档拆分策略"使用
- 配合"验证优先工作流"使用
