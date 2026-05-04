---
name: session-startup
description: Use when starting a new conversation session to load current project progress and pending todos
---

# 会话启动

> 每次会话开始时调用，加载当前工作进度和待办事项。
> 如果项目中配置了 SessionStart hook，hook 已自动输出进度摘要，此 skill 作为补充上下文按需加载。

## 执行步骤

1. 读取以下文件（限制行数避免上下文浪费）：
   - `.claude/schedule/schedule.md` — 只读前 20 行（标题 + 当前状态 + 最近阶段）
   - `.claude/todos/todos.md` — 只读前 40 行（活跃待办）
2. 向用户输出格式化状态摘要

## 输出模板

```
当前进度：{从 schedule.md 提取「当前状态」行}
待办事项：{从 todos.md 提取活跃 TODO，2-3 条关键项}
建议下一步：{基于当前状态和待办推断}
```

## 文件不存在时的处理

如果文件不存在，提示用户并建议初始化：

- **schedule.md**：
  ```markdown
  # {项目名} 工作进度
  > 更新: {今天日期}
  ## 当前状态：项目初始化
  ```

- **todos.md**：
  ```markdown
  # 待办事项
  > 更新: {今天日期}
  ## 待办事项
  ```

## 注意事项

- 只加载进度和待办，不加载架构文档等大文件
- 汇报用 2-3 句话概括，不逐条朗读
- 如果启动 hook 已输出进度摘要，跳过重复输出
- 长文件只读前几行获取最新状态，不全文加载
