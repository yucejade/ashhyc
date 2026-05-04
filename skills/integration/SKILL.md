---
name: integration
description: Use when committing code, merging branches, creating PRs, or CI/CD
---

# 集成与部署 Skill

> 提交代码、合并分支、创建 PR、CI/CD 时加载此文件。

## Git 策略

- `master` / `main` — 稳定代码，每次 push 可运行
- `feature/*` — 新功能，完成后 PR 合并
- 每阶段打 tag：`v0.1.0`、`v0.2.0` ...

## 提交规范

1. **小步提交**：每完成一个函数/类就测试
2. **信息格式**：`[模块名] 简述`，如 `[core] 实现EventBus发布功能`
3. **测试先行**：先提交测试文件，再提交实现

## 质量检查流程

```
代码 + 测试 → lint+format → 类型检查 → 全量测试 → 自我审查 → verify脚本 → 用户确认
```

## 提交前检查

### 工具检查
- [ ] `ruff check .` + `ruff format .` 通过
- [ ] `mypy` 零错误
- [ ] `bandit` 无告警
- [ ] 全部测试通过
- [ ] 覆盖率 > 85%

### 代码审查
- [ ] 文件 ≤ 200 行 / 类型注解完整 / 无空 except / 无硬编码 / 无全局变量 / 无敏感信息
- [ ] 依赖方向正确 / 事件解耦 / 基类接口未改
- [ ] 异常路径覆盖 / 边界值测试

### 性能审查
- [ ] handler ≤ 100ms / 单阶段链 ≤ 1s
