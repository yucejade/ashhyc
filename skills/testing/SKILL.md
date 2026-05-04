---
name: testing
description: Use when writing or running tests
---

# 测试 Skill

> 编写或运行测试时加载此文件。

## 测试约束

1. **先写测试** — 关键流程先写测试，再写实现
2. **独立性** — 每个用例独立，不依赖执行顺序
3. **Mock 外部** — 外部依赖（API/数据库/通知渠道）必须 Mock
4. **异常覆盖** — 每个 try/except 都有异常路径测试
5. **边界值** — 数值计算（费率、仓位）必须测试
6. **覆盖率** — 核心代码 > 85%

## 测试目录

```
tests/
├── unit/           # 每个模块一个 test_*.py
├── integration/    # 跨模块交互
└── conftest.py     # 共享 fixtures
```

## 性能测试（pytest-benchmark）

```python
def test_xxx_performance(benchmark):
    result = benchmark(target_function, args)
```

**阈值**：单次 handler > 100ms → 优化 | 单阶段链 > 1s → 优化

## 异常场景

外部系统断连 / 配置缺失 / 写入冲突 / 插件加载失败 / 网络中断恢复

## 验证脚本

`verify_*.py` — 功能 + 性能 + 边界 + 异常的集成验证

## 测试后检查

- [ ] 所有测试通过
- [ ] 覆盖率 > 85%
- [ ] 异常路径已覆盖
- [ ] 边界值已测试
- [ ] 性能基准未退化
