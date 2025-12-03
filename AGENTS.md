# AGENTS.md

> 本文件为 AI 编码代理提供项目上下文和工作指南。参考 [agents.md](https://agents.md/) 规范。

## Project Overview

Scheduled Service 是一个基于 BullMQ 的定时任务服务，作为基于大模型的端对端语音聊天助手的定时服务工具的前置测试项目。

**核心能力**：
- ⏰ 定时提醒与通知
- 🔄 周期性任务执行
- ⏳ 延迟任务处理
- 📊 任务状态管理与监控

## Setup Commands

```bash
# 安装依赖（必须使用 pnpm）
pnpm install

# 启动 Redis（使用 Docker）
docker run -d --name redis -p 6379:6379 redis:latest

# 配置环境变量
cp .env.example .env
```

## Development Commands

```bash
# 启动开发服务器
pnpm dev

# 构建生产版本
pnpm build

# 运行测试
pnpm test

# 代码检查
pnpm lint
```

## Code Style

- **Language**: TypeScript (strict mode)
- **Indent**: 2 spaces
- **Quotes**: Single quotes `'`
- **Semicolons**: Required
- **Comments**: 英文为主，中文补充（复杂逻辑可用中文解释）

### Naming Conventions

| 类型 | 风格 | 示例 |
|------|------|------|
| 变量/函数 | camelCase | `getUserById`, `isActive` |
| 类/接口 | PascalCase | `ReminderQueue`, `JobOptions` |
| 文件名 | kebab-case | `reminder-queue.ts` |
| 常量 | UPPER_SNAKE | `MAX_RETRY_COUNT` |

## Testing Instructions

- 测试框架：Vitest
- 测试文件位于 `tests/` 目录
- 运行单个测试：`pnpm vitest run -t "<test name>"`
- 提交前必须通过所有测试：`pnpm test`
- 修改代码时同步更新或添加相关测试

## Project Structure

```
scheduled-service/
├── src/
│   ├── queues/            # BullMQ 队列定义
│   ├── workers/           # Worker 处理器
│   ├── jobs/              # 任务定义
│   ├── utils/             # 工具函数
│   └── index.ts           # 入口文件
├── tests/                  # 测试文件
├── config/                 # 配置文件
├── AGENTS.md              # Agent 指南（本文件）
├── package.json
├── tsconfig.json
└── .env.example
```

## Tech Stack

| 技术 | 用途 |
|------|------|
| Node.js | 运行时 |
| TypeScript | 开发语言 |
| BullMQ | 任务队列 |
| Redis | 队列存储 |
| Vitest | 测试框架 |
| pnpm | 包管理器 |
| UV | Python 虚拟环境（如需要） |

## BullMQ Conventions

### Queue Naming
- 使用有意义的名称：`reminder-queue`, `notification-queue`
- 不同任务类型使用不同队列

### Job Definition
- Job 数据包含完整上下文
- 设置合理的重试策略（attempts, backoff）
- 关键任务设置失败回调

### Worker Implementation
- 保持幂等性
- 正确处理失败和重试
- 记录详细日志

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

### 推荐工作流

#### 1. 探索-计划-编码-提交 (Explore-Plan-Code-Commit)
适用于大多数功能开发：
1. **探索**: 先阅读相关文件，理解现有架构（不要急于写代码）
2. **计划**: 使用 "think" 触发深度思考，制定实现方案
3. **编码**: 按计划实现，边实现边验证
4. **提交**: 完成后提交代码，更新相关文档

#### 2. 测试驱动开发 (TDD)
适用于逻辑复杂、需要验证的功能：
1. 先编写测试用例，明确预期输入输出
2. 确认测试失败（功能未实现）
3. 编写代码使测试通过
4. 重构优化，保持测试通过
5. 提交测试和代码

#### 3. 迭代优化
适用于 UI 或需要可视化验证的功能：
1. 实现初版功能
2. 检查结果（截图、日志等）
3. 根据反馈迭代改进
4. 满意后提交

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
