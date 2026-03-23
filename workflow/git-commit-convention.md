# Git Commit 规范

**适用场景**: prototype, production, refactor

## Commit 消息格式

```
<type> <action>: <description>

[optional body]

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
```

## Type 分类

| Type | 说明 | 示例 |
|------|------|------|
| `<feature>` | 新功能 | `<feature> add: wave system` |
| `<fix>` | Bug 修复 | `<fix> modify: boss HP calculation` |
| `<refactor>` | 重构 | `<refactor> modify: extract domain logic` |
| `<doc>` | 文档 | `<doc> add: architecture guide` |
| `<asset>` | 资产 | `<asset> add: monster configs` |
| `<misc>` | 其他 | `<misc> modify: update .gitignore` |

## Action 分类

| Action | 说明 |
|--------|------|
| `add` | 新增文件/功能 |
| `modify` | 修改现有内容 |
| `remove` | 删除文件/功能 |

## 示例

```
<feature> add: ammo pickup system

Add ammo pickup spawn, collection, and UI arrow indicator.

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
```

```
<refactor> modify: extract UI boundary layer

Move logic-to-UI calls through UIApp instead of direct panel access.

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
```

## 与其他方法的关系

- 是"verification-first workflow"的一部分
- 配合"slice-based refactor"使用
