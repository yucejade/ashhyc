# ashhyc

Claude Code 个人自定义 Skills 插件，提供通用的项目管理工作流和开发规范。

## 功能

| Skill | 调用名 | 用途 |
|-------|--------|------|
| 会话启动 | `ashhyc:session-startup` | 新会话开始时加载项目进度和待办事项 |
| 会话退出 | `ashhyc:session-exit` | 会话结束前保存进度/待办/QA/决策/文档索引 |
| 设计评审 | `ashhyc:design-review` | 三角色结构化评审（PM/Dev/QA）+ 交叉验证 |
| 文档 | `ashhyc:documentation` | 编写/修改设计文档时的通用规则和检查清单 |
| 编码 | `ashhyc:coding` | 编写/修改代码时的通用约束和检查清单 |
| 测试 | `ashhyc:testing` | 编写/运行测试时的通用约束和检查清单 |
| 集成 | `ashhyc:integration` | 提交代码、合并分支、CI/CD 的通用规范 |

### Hooks（自动触发）

| Hook | 触发时机 | 用途 |
|------|---------|------|
| SessionStart | 会话启动、`/clear`、`/compact` | 自动注入项目进度和待办状态到会话上下文 |
| Stop | 每次响应结束后 | 检测 `.claude/` 未保存修改并提醒 |

### 双层 Skill 架构

本插件提供**通用层**规则，项目可通过本地 `.claude/skills/` 添加**项目层**补充：

```
插件通用层（ashhyc）          项目特有层（.claude/skills/）
├── 文档修改原则              ├── 项目特有的开发环境配置
├── 写作规范                  ├── 项目特有的设计约束
├── 编码约束                  ├── 项目特有的字段注释格式
├── 测试约束                  ├── 项目特有的工具链命令
├── 集成策略                  └── 项目特有的上下文引用
└── 会话管理
```

## 安装

在 Claude Code 中通过 marketplace 安装：

1. 确保全局配置 `~/.claude/settings.json` 中包含：
   ```json
   "extraKnownMarketplaces": {
     "ashhyc": {
       "source": {
         "source": "github",
         "repo": "yucejade/ashhyc"
       }
     }
   }
   ```

2. 在项目配置 `.claude/settings.json` 中启用：
   ```json
   "enabledPlugins": {
     "ashhyc@ashhyc": true
   }
   ```

3. 重启 Claude Code 会话，skills 会自动注册。

## 使用

### 会话管理

在项目的 `CLAUDE.md` 中添加启动/退出行为：

```markdown
## 启动行为
1. 调用 `superpowers:using-superpowers` 加载超能力
2. 调用 `ashhyc:session-startup` 加载进度和待办

## 退出行为
当用户表达结束意图时，调用 `ashhyc:session-exit` 保存工作状态
```

### 设计评审

```bash
ashhyc:design-review              # 单次模式，单章节
ashhyc:design-review loop         # 循环模式，直到零问题
ashhyc:design-review single full  # 单次模式，全文档
ashhyc:design-review loop full    # 循环模式，全文档
```

### 项目文件约定

会话管理依赖以下项目文件结构（不存在时会提示初始化）：

```
.claude/
├── schedule/schedule.md    # 工作进度
├── todos/todos.md          # 待办事项
├── QA/                     # 讨论记录
│   └── decision/           # 设计决策
└── review/                 # 审查结果
docs/
└── architecture/
    └── index.md            # 文档索引（可选）
```

### 可靠性保障

插件内置 Stop hook，自动检测 `.claude/` 目录未保存的修改并提醒。无需手动配置。

### Hooks 详细说明

**SessionStart Hook**
- 读取 `.claude/schedule/schedule.md`（前20行）和 `.claude/todos/todos.md`（前40行）
- 将项目状态摘要注入到会话上下文中
- 文件不存在时输出初始化指引，不影响会话正常使用

**Stop Hook**
- 检查 `.claude/` 目录是否有未保存的 git 修改（未暂存/已暂存/未跟踪）
- 有修改时输出保存提醒
- 非 git 项目静默跳过

> Hook 是自动触发的，无需手动调用。如需更详细的会话管理，仍可手动调用 `ashhyc:session-startup` 和 `ashhyc:session-exit` skills。

## 版本历史

### v1.2.0
- 新增 SessionStart hook：自动注入项目进度和待办状态到会话上下文
- 新增 Stop hook：检测未保存的 `.claude/` 修改并提醒保存
- 添加 `.gitattributes` 确保 hook 脚本跨平台兼容
- 移除手动 Stop hook 配置说明（已内置）

### v1.1.0
- 新增 `session-startup`、`session-exit` 会话管理 skills
- 新增 `documentation`、`coding`、`testing`、`integration` 通用任务 skills
- 更新 README

### v1.0.0
- 初始版本
- `design-review` 三角色设计评审 skill
