# 新项目接入全局知识库指南

## 快速接入（3 步）

### 1. 在项目文档目录创建引用文件

在项目的 `docs/` 或 `.claude/docs/` 目录下创建 `GLOBAL_DOCS_REFERENCE.md`：

```markdown
# 全局知识库引用

本项目使用全局知识库，避免重复维护通用知识。

## 全局库位置

D:\AI\Claude\GlobalDocs

远端：git@github.com:onovich/CommonGameDevVibeCodingDocSystem.git

## 引用方式

优先查阅全局库：

### 工作流类
- 低 churn 重构、验证优先、slice-based refactor
- 薄 skill + 厚文档、文档拆分、AI 低 token 工作流
- 阶段化功能开发、Git commit 规范

### 架构类
- System+Domain、原型收敛、UI 边界
- Factory+Pool+Repo、组合根、配置驱动、事件通信

### Unity 专属
- batchmode 验证、资源加载、生命周期坑
- 对象池、SO 配置

### 项目类型
- Survivor-like 架构

## 本地文档范围

本项目 docs/ 只保留：
- 项目特有约束
- 当前架构锚点
- 本地资源路径
- 历史遗留问题
```

### 2. 在项目文档索引中添加全局库入口

在 `docs/INDEX.md` 或 `docs/README.md` 开头添加：

```markdown
## 全局知识库

通用知识见 D:\AI\Claude\GlobalDocs，详见 GLOBAL_DOCS_REFERENCE.md。

## 本地文档列表
...
```

### 3. 在 CLAUDE.md 中提示 AI 优先读全局库

在项目根目录的 `CLAUDE.md` 中添加：

```markdown
## 全局知识库

本项目使用全局知识库 D:\AI\Claude\GlobalDocs。

AI 工作时：
- 优先查阅全局库的通用知识
- 本地文档只保留项目特有内容
- 如需了解通用方法论，见 docs/GLOBAL_DOCS_REFERENCE.md
```

---

## 完整接入（推荐）

### 步骤 1：克隆全局库（可选）

如果需要离线访问或贡献内容：

```bash
cd D:\AI\Claude
git clone git@github.com:onovich/CommonGameDevVibeCodingDocSystem.git GlobalDocs
```

### 步骤 2：创建项目文档结构

```
your-project/
├── docs/
│   ├── INDEX.md                    # 文档索引
│   ├── GLOBAL_DOCS_REFERENCE.md    # 全局库引用
│   ├── architecture.md             # 项目特有架构
│   └── ...
└── CLAUDE.md                       # 项目根指引
```

### 步骤 3：编写项目特有文档

只写项目特有内容，例如：

**docs/architecture.md**：
```markdown
# 项目架构

**通用架构模式见全局库**：
- D:\AI\Claude\GlobalDocs\architecture\system-domain-pattern.md
- D:\AI\Claude\GlobalDocs\architecture\composition-root-pattern.md

## 本项目特有架构

### 代码锚点
- src/Main.cs
- src/GameSystem.cs
- src/UIApp.cs

### 项目特有约束
- 使用 Addressables 加载资源
- 配置文件在 Assets/Configs/
```

### 步骤 4：创建项目 skill（可选）

在 `.claude/skills/` 下创建项目 skill，引用全局方法：

```markdown
---
name: project-refactor
description: Refactor this project toward target architecture
---

# project-refactor

## Behavior

1. 先读全局方法论：
   - D:\AI\Claude\GlobalDocs\workflow\slice-based-refactor.md
   - D:\AI\Claude\GlobalDocs\architecture\prototype-to-production.md

2. 再读项目特有文档：
   - docs/architecture.md
   - docs/workflow.md

3. 按项目代码锚点执行重构

## 项目特有规则
- 使用 Addressables，不用 Resources
- 配置在 Assets/Configs/
```

---

## AI 使用方式

### 方式 1：直接引用全局文档

在对话中直接告诉 AI：

```
请参考全局库 D:\AI\Claude\GlobalDocs\workflow\low-churn-refactor.md
进行重构
```

### 方式 2：通过项目文档间接引用

项目文档中写：

```markdown
**通用方法见全局库**：D:\AI\Claude\GlobalDocs\workflow\xxx.md

## 本项目特有部分
...
```

AI 会先读全局库，再读项目特有内容。

### 方式 3：通过 skill 自动引用

创建项目 skill，在 skill 中指定阅读顺序：

```markdown
1. 先读：D:\AI\Claude\GlobalDocs\workflow\xxx.md
2. 再读：docs/project-specific.md
```

---

## 贡献新知识到全局库

### 发现通用知识时

1. 判断是否通用：
   - 可跨项目复用？
   - 不依赖项目特定路径/命名？
   - 适用于多个项目类型？

2. 提交到全局库：

```bash
cd D:\AI\Claude\GlobalDocs
# 编辑或新增文档
git add .
git commit -m "Add: xxx pattern"
git push
```

3. 在项目中引用：

```markdown
**通用方法见全局库**：D:\AI\Claude\GlobalDocs\xxx.md
```

---

## 示例：Unity 新项目接入

### 1. 创建引用文件

`docs/GLOBAL_DOCS_REFERENCE.md`（复制模板）

### 2. 编写项目架构文档

`docs/architecture.md`：
```markdown
# 项目架构

**通用模式见全局库**：
- System+Domain: GlobalDocs\architecture\system-domain-pattern.md
- Factory+Pool+Repo: GlobalDocs\architecture\factory-pool-repo-pattern.md

## 本项目代码锚点
- Assets/Scripts/Main.cs
- Assets/Scripts/GameSystem/
- Assets/Scripts/UIApp/
```

### 3. 编写项目工作流文档

`docs/workflow.md`：
```markdown
# 工作流

**通用工作流见全局库**：
- 验证优先: GlobalDocs\workflow\verification-first-workflow.md
- Unity 验证: GlobalDocs\unity\batchmode-verification.md

## 本项目验证命令
Unity.exe -quit -batchmode -projectPath <path> -logFile <log>
```

### 4. 更新 CLAUDE.md

```markdown
## 全局知识库

本项目使用 D:\AI\Claude\GlobalDocs。
详见 docs/GLOBAL_DOCS_REFERENCE.md。
```

完成。新项目已接入全局知识库。
