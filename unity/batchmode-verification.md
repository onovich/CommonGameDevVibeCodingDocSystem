# Unity 无头编译验证流程

**适用场景**: prototype, production, refactor

## 为什么需要无头编译

### 问题场景

- 依赖 Unity Editor 内编译，无法自动化
- 新增文件后 Editor 方法可能失败
- 难以集成到 CI/CD

### 解决方案

使用 Unity batchmode 无头编译：
- 可自动化
- 可集成到 CI/CD
- 可在 AI 辅助开发中快速验证

## 两步编译规则

### 为什么需要两步

Unity batchmode 的固有约束：
- 新增 `.cs` 文件后，Editor 方法可能找不到新类型
- 必须先纯编译，再执行 Editor 方法

### Step 1: 纯编译检查

```bash
Unity.exe \
  -quit -batchmode \
  -projectPath <project_path> \
  -logFile <log_path> \
  -buildTarget StandaloneWindows64
```

目的：
- 验证代码编译通过
- 让 Unity 识别新增的类型

### Step 2: 执行 Editor 方法

```bash
Unity.exe \
  -quit -batchmode \
  -projectPath <project_path> \
  -logFile <log_path> \
  -executeMethod <ClassName.MethodName>
```

目的：
- 执行 Builder 方法
- 生成资产/场景

## 何时需要两步

### 必须两步
- 新增 `.cs` 文件
- 新增类型定义
- 新增 Editor 方法

### 可以只做 Step 1
- 只修改现有代码
- 只做运行时结构调整
- 不涉及 Editor 方法

## 日志分析

### 失败模式

```
error CS0246: The type or namespace name 'XXX' could not be found
Compilation failed: X error(s)
Scripts have compiler errors
Aborting batchmode due to failure
```

### 成功模式

```
Tundra build success
Compilation completed successfully
```

## 与其他方法的关系

- 是"verification-first workflow"的具体实施
- 配合"slice-based refactor"使用
