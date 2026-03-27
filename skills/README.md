# Skills 目录说明

本目录包含可复用的 skill 模板。

## Skill 设计原则

参考 `workflow/thin-skill-heavy-doc.md`。

## 目录结构

| 目录 | 用途 |
|------|------|
| `.opencode/skills/` | OpenCode 原生 skill |
| `.claude/skills/` | Claude Code 原生 skill（OpenCode 也兼容读取） |

## 推荐 Skill 模板

### 通用 Skill

- `low-churn-refactor` - 低 churn 重构调度
- `verify-compile` - 编译验证
- `phased-feature` - 阶段化功能开发
- `tool-config-migrate` - AI 工具配置迁移

### Unity Skill

- `unity-refactor-playbook` - Unity 原型收敛
- `unity-batchmode-verify` - Unity 无头编译验证

## 使用方式

将 skill 模板复制到对应工具的配置目录下：
- OpenCode 项目：`.opencode/skills/`
- Claude Code 项目：`.claude/skills/`
