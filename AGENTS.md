# Repository Guidelines

Scheduled Service 是一个基于 BullMQ 的定时任务服务，作为基于大模型的端对端语音聊天助手的定时服务工具的前置测试项目。

## Project Overview

本项目旨在为语音聊天助手提供可靠的定时任务调度能力，支持：
- 定时提醒与通知
- 周期性任务执行
- 延迟任务处理
- 任务状态管理与监控

## Tech Stack

- **Runtime**: Node.js
- **Queue System**: BullMQ (基于 Redis)
- **Language**: TypeScript
- **Python Environment**: UV (如需 Python 环境)

## Project Structure & Module Organization

```
scheduled-service/
├── src/                    # 源代码目录
│   ├── queues/            # BullMQ 队列定义
│   ├── workers/           # Worker 处理器
│   ├── jobs/              # 任务定义
│   ├── utils/             # 工具函数
│   └── index.ts           # 入口文件
├── tests/                  # 测试文件
├── config/                 # 配置文件
├── scripts/                # 脚本文件
├── AGENTS.md              # Agent 指南（本文件）
├── package.json           # Node.js 依赖配置
├── tsconfig.json          # TypeScript 配置
└── .env.example           # 环境变量示例
```

## Build, Test, and Development Commands

### Node.js / TypeScript
- **包管理器**: 使用 pnpm（不要使用 npm 或 yarn）
- `pnpm install`: 安装项目依赖
- `pnpm dev`: 启动开发服务器
- `pnpm build`: 构建生产版本
- `pnpm test`: 运行测试（使用 vitest）
- `pnpm lint`: 代码检查

### Python (如需要)
- `uv venv`: 创建虚拟环境
- `uv pip install -r requirements.txt`: 安装 Python 依赖
- `uv run python <script.py>`: 在虚拟环境中运行 Python 脚本

### Redis (BullMQ 依赖)
- 使用 Docker 部署 Redis 服务
- `docker run -d --name redis -p 6379:6379 redis:latest`: 启动 Redis 容器
- `docker start redis`: 启动已存在的 Redis 容器
- `docker stop redis`: 停止 Redis 容器

## Coding Style & Naming Conventions

### TypeScript
- 使用 ESLint + Prettier 进行代码格式化
- **缩进**: 2 个空格
- **引号**: 使用单引号 `'`
- **分号**: 语句末尾使用分号
- **注释语言**: 英文为主，中文补充（复杂逻辑可用中文解释）
- 使用 camelCase 命名变量和函数
- 使用 PascalCase 命名类和接口
- 文件名使用 kebab-case 或 camelCase
- 优先使用 async/await 处理异步操作
- 为函数添加类型注解

### Python
- 遵循 PEP 8 规范
- 使用 snake_case 命名变量和函数
- 使用 PascalCase 命名类
- 使用 UV 管理虚拟环境

## BullMQ 使用规范

### 队列命名
- 使用有意义的队列名称，如 `reminder-queue`, `notification-queue`
- 不同类型的任务使用不同的队列

### Job 定义
- Job 数据应包含足够的上下文信息
- 设置合理的重试策略和超时时间
- 为关键任务设置失败回调

### Worker 实现
- Worker 应保持幂等性
- 正确处理任务失败和重试
- 记录详细的日志信息

## Testing Guidelines

- 测试文件放在 `tests/` 目录下
- 测试函数以 `test_` 开头（Python）或使用 `describe/it` 模式（TypeScript）
- 为关键业务逻辑编写单元测试
- 为队列处理编写集成测试

## Git Commit Guidelines

### 提交格式

```
<emoji> <type>(<scope>): <subject>

WHAT: <一句话描述动作与对象>

WHY: <业务目标、用户需求或缺陷背景>

HOW: <实现策略、兼容性影响、验证方式>
```

### Type 与 Emoji 映射

| Type | Emoji | 说明 |
|------|-------|------|
| feat | ✨ | 新功能 |
| fix | 🐛 | 修复 Bug |
| docs | 📝 | 文档变更 |
| style | 🎨 | 代码格式（不影响逻辑） |
| refactor | ♻️ | 重构（非新功能、非修复） |
| perf | ⚡️ | 性能优化 |
| test | ✅ | 测试相关 |
| build | 🏗️ | 构建系统或外部依赖 |
| ci | 🤖 | CI/CD 配置 |
| chore | 🧹 | 杂务（不修改 src 或 test） |
| revert | ⏪ | 回滚提交 |

### 规范要求

- **标题长度**: 不超过 50 字符
- **标题语言**: 中文为主，英文补充
- **标题末尾**: 不加标点
- **正文结构**: 必须包含 WHAT/WHY/HOW 三段式
- **关联 Issue**: 在 WHY 中使用 `#issue_number` 关联

### 示例

```
✨ feat(queue): 添加定时提醒队列

WHAT: 新增 reminder-queue 队列及对应 Worker

WHY: 为语音助手提供定时提醒能力，支持用户设置提醒任务 #1

HOW: 基于 BullMQ delayed job 实现，支持延迟执行和 cron 表达式
```

## Security & Configuration Tips

- 将 `.env.example` 复制为 `.env` 并配置本地环境变量
- 不要提交包含敏感信息的文件（如 `.env`, Redis 密码等）
- 在 `.gitignore` 中忽略敏感文件和构建产物

## Automation & Agent Workflow

### Agent 操作规范
- 使用绝对路径执行 shell 命令
- 修改代码前先了解现有代码结构
- 保留用户的本地修改，不要随意重置
- 复杂任务需要制定多步计划

### ⚠️ 对话成本控制（重要）

**背景**: 用户使用 Copilot 按消息计费，每次用户发送新消息都会产生费用。

**核心原则**:
1. **一轮对话尽量多做事**: 不要做完一件小事就停下来问用户，应该持续推进直到任务完成或确实需要用户决策
2. **使用命令行交互获取输入**: 如需用户确认或输入信息，使用 `Read-Host` 在终端中交互，而不是结束回复
3. **等待命令执行完成**: 当终端命令需要用户输入时（如 `Read-Host`），必须等待命令执行完毕并获取结果后再继续，绝不能在命令执行中途结束回复
4. **批量处理**: 能一次完成的操作不要分多次
5. **禁止以问题结尾**: 回复的最后不要以开放性问题结尾（如"还需要什么？"、"要继续吗？"），这会迫使用户发送新消息。如果确实需要用户决策，必须通过 `Read-Host` 在命令行中获取

**错误示例**:
- ❌ 执行了 `Read-Host` 后立即结束回复，没有等待用户输入
- ❌ 创建一个文件后就停下来问"还需要做什么吗？"
- ❌ 每完成一个小步骤就结束回复等用户确认
- ❌ 回复末尾写"你还有其他想要补充的吗？"然后结束
- ❌ 修改完代码后说"你可以运行 pnpm build 来编译"然后结束，把操作留给用户

**正确示例**:
- ✅ 执行 `Read-Host` 后持续等待直到获取用户输入，然后继续处理
- ✅ 根据任务需求连续创建多个文件、安装依赖、运行测试
- ✅ 遇到真正需要用户决策的分歧点时通过命令行询问，获取答案后继续执行
- ✅ 任务完成后简洁总结，不追问后续
- ✅ 修改完代码后通过命令行询问"是否需要编译/测试？(y/n)"，用户回复后立即执行

### 代码修改原则
- 修改前运行相关测试确保现有功能正常
- 修改后运行测试验证变更正确
- 添加必要的注释说明复杂逻辑
- 遵循现有代码风格和项目约定

## Dependencies

### 核心依赖
- `bullmq`: 任务队列
- `ioredis`: Redis 客户端
- `typescript`: TypeScript 支持

### 开发依赖
- `tsx` / `ts-node`: TypeScript 运行时
- `jest` / `vitest`: 测试框架
- `eslint`: 代码检查
- `prettier`: 代码格式化

## Roadmap

- [ ] 初始化项目结构
- [ ] 配置 BullMQ 基础设施
- [ ] 实现基本的定时任务功能
- [ ] 添加任务监控和日志
- [ ] 与语音聊天助手集成
- [ ] 完善测试覆盖

## LLM API Configuration

项目常用的测试大模型 API 配置：

| 用途      | 服务商   | 模型               | Binding |
| --------- | -------- | ------------------ | ------- |
| LLM       | 智谱 AI  | GLM-4-Flash        | openai  |
| Embedding | Gitee AI | BGE-M3             | openai  |
| Rerank    | Gitee AI | BGE-Reranker-v2-M3 | cohere  |

### 配置示例

```bash
# LLM: GLM-4-Flash (智谱 AI)
LLM_BINDING=openai
LLM_MODEL=glm-4-flash-250414
LLM_BINDING_HOST=https://open.bigmodel.cn/api/paas/v4

# Embedding: BGE-M3 (Gitee AI)
EMBEDDING_BINDING=openai
EMBEDDING_MODEL=bge-m3
EMBEDDING_DIM=1024
EMBEDDING_BINDING_HOST=https://ai.gitee.com/v1

# Rerank: BGE-Reranker-v2-M3 (Gitee AI)
RERANK_BINDING=cohere
RERANK_MODEL=bge-reranker-v2-m3
RERANK_BINDING_HOST=https://ai.gitee.com/v1/rerank
```

## Notes

- 本项目为前置测试项目，用于验证定时服务架构
- 后续将集成到主项目中作为定时服务模块
- 如遇到问题，请参考 BullMQ 官方文档: https://docs.bullmq.io/
