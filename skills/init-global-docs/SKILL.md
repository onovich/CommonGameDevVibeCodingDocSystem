---
name: init-global-docs
description: Initialize global docs reference for new project
tags: [setup, docs, global]
---

# init-global-docs

为新项目初始化全局知识库引用。

## Usage

```
/init-global-docs
```

## Behavior

1. 检查项目是否已有 `docs/GLOBAL_DOCS_REFERENCE.md`
2. 如果没有，创建该文件
3. 在 `docs/INDEX.md` 或 `docs/README.md` 中添加全局库入口
4. 在 `CLAUDE.md` 中添加全局库提示
5. 输出完成提示

## 创建的文件

### docs/GLOBAL_DOCS_REFERENCE.md

```markdown
# 全局知识库引用

本项目使用全局知识库，避免重复维护通用知识。

## 全局库位置

D:\AI\Claude\GlobalDocs

远端：git@github.com:onovich/CommonGameDevVibeCodingDocSystem.git

## 引用方式

优先查阅全局库通用知识。

详见 D:\AI\Claude\GlobalDocs\HOW_TO_USE.md
```

### 更新 docs/INDEX.md

在开头添加：

```markdown
## 全局知识库

通用知识见 D:\AI\Claude\GlobalDocs，详见 GLOBAL_DOCS_REFERENCE.md。
```

### 更新 CLAUDE.md

添加：

```markdown
## 全局知识库

本项目使用 D:\AI\Claude\GlobalDocs。
详见 docs/GLOBAL_DOCS_REFERENCE.md。
```

## 输出

```
已初始化全局知识库引用：
- 创建 docs/GLOBAL_DOCS_REFERENCE.md
- 更新 docs/INDEX.md
- 更新 CLAUDE.md

全局库位置：D:\AI\Claude\GlobalDocs
使用指南：D:\AI\Claude\GlobalDocs\HOW_TO_USE.md
```
