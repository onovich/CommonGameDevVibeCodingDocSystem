---
name: tool-config-migrate
description: Migrate tool config between AI coding agents (Claude Code ↔ OpenCode)
tags: [setup, config, migration, claude-code, opencode]
---

# tool-config-migrate

将一个 AI 编码工具的配置迁移到另一个工具，实现多工具交替管理同一项目。

## 适用场景

- 新项目接入 OpenCode 时，基于 Claude Code 的 CLAUDE.md 生成 AGENTS.md
- 新项目接入 Claude Code 时，基于 OpenCode 的 AGENTS.md 生成 CLAUDE.md
- 同步两个工具的配置，保持一致性

## 执行流程

### 1. 理解源配置

读取源工具的配置文件：
- **Claude Code**：`CLAUDE.md` + `.claude/skills/` + `.claude/docs/`
- **OpenCode**：`AGENTS.md` + `.opencode/skills/` + `.claude/skills/`（兼容）

提取关键信息：
- 项目概述与架构描述
- 目录结构
- 命名空间约定
- 关键模式与规范
- Skills 与 Commands

### 2. 生成目标配置

根据源配置生成目标工具的配置文件：

**OpenCode 的 AGENTS.md 必需内容**：
```markdown
# AGENTS.md

## 项目概述
描述项目类型、技术栈、目标平台

## 构建与开发
说明构建命令、开发环境

## 架构
- 入口点
- 分层结构
- 核心模式（System+Domain、App 模式等）
- 跨系统通信方式

## 目录结构
代码组织方式

## Skills
指向 .claude/skills/ 目录（OpenCode 自动兼容）

## 参考文档
指向详细设计文档
```

**Claude Code 的 CLAUDE.md 必需内容**：
- 项目概述
- 构建与开发说明
- 架构说明
- 目录结构
- 参考文档链接

### 3. 同步 Skills

OpenCode 兼容 `.claude/skills/` 目录，Claude Code 兼容 `.claude/skills/` 目录。**无需迁移 skills**，两个工具共享同一套 skills。

### 4. Git Commit 规范

工具配置文件属于 `<misc>` 类型：
```
<misc> add: AGENTS.md for OpenCode compatibility
```

不是 `<doc>`（业务文档），不是 `<config>`。

### 5. 提交并推送

```bash
git add AGENTS.md CLAUDE.md  # 根据实际修改的文件
git commit -m "<misc> add: AGENTS.md for OpenCode compatibility"
git push
```

## 示例

将 Claude Code 的 Scaffold 项目配置迁移到 OpenCode：

**源**：Scaffold/CLAUDE.md（Unity 游戏项目架构说明）
**目标**：Scaffold/AGENTS.md（保持相同内容结构，适配 OpenCode 格式）

## 注意事项

1. **不要重复造轮子**：OpenCode 自动发现 `.claude/skills/`，无需复制 skills
2. **保持一致性**：两个配置文件的项目概述、架构描述应保持同步
3. **commit 类型**：工具配置用 `<misc>`，业务文档用 `<doc>`
4. **文档引用**：详细规范放在 `.claude/docs/`，主配置只做概要引用
