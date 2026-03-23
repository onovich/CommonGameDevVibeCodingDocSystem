# 文档拆分策略

**适用场景**: prototype, production, refactor

## 为什么拆分文档

### 问题
- 单个文档过大，AI 一次读取消耗大量 token
- 文档混杂多个主题，AI 难以定位需要的部分
- 文档更新时容易产生冲突

### 优势
- 按需读取：只读当前需要的部分
- 降低 token 消耗：不读无关内容
- 易于维护：每个文档聚焦一个主题
- 易于版本管理：减少冲突

## 拆分原则

### 1. 按主题拆分

每个文档聚焦一个主题：
- 架构定义
- 工作流
- 重构手册
- 经验教训
- 技能清单

### 2. 按适用场景拆分

- 原型阶段文档
- 生产阶段文档
- 重构阶段文档

### 3. 按引擎拆分

- Unity 专属
- Godot 专属
- Bevy 专属
- 跨引擎通用

## 推荐结构

### 项目级文档
```
docs/
├── INDEX.md                    # 唯一索引入口
├── architecture.md             # 架构定义
├── workflow.md                 # 工作流
├── prototype-experience.md     # 原型阶段经验
├── refactor-playbook.md        # 重构手册
└── lessons.md                  # 经验教训
```

### 全局知识库
```
GlobalDocs/
├── README.md
├── workflow/
│   ├── README.md
│   ├── slice-based-refactor.md
│   ├── verification-first-workflow.md
│   └── low-churn-refactor.md
├── unity/
│   ├── README.md
│   └── ...
└── architecture/
    ├── README.md
    └── ...
```

## 索引文档规范

### 唯一索引入口

项目内只有一个文档可以列出所有文档：`docs/INDEX.md`

其他文档不得重复建立类似列表。

### 索引格式

```markdown
# 文档速查表

按需查阅，不要一次性读取所有文档。

## 文档列表

- architecture.md — 架构定义
- workflow.md — 工作流
- refactor-playbook.md — 重构手册
```

## 文档内引用规范

### 内部文档引用

使用纯文本路径，不用 Markdown 超链接：

```markdown
目标结构见 docs/architecture.md。
验证流程见 docs/workflow.md。
```

不要：
```markdown
目标结构见 [architecture.md](architecture.md)。
```

### 外部 URL 引用

外部 URL 不受此限制，可以使用 Markdown 超链接。

## 与其他方法的关系

- 是"AI 低 token 工作流"的基础
- 配合"薄 skill + 厚文档"模式使用
