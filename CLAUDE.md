# CLAUDE.md

> Claude Code 专用配置文件。提供项目上下文、常用命令和工作流指南。

## Bash Commands

```bash
# 包管理（必须使用 pnpm）
pnpm install          # 安装依赖
pnpm dev              # 启动开发服务器
pnpm build            # 构建生产版本
pnpm test             # 运行测试
pnpm lint             # 代码检查

# Docker Redis
docker run -d --name redis -p 6379:6379 redis:latest  # 首次启动
docker start redis    # 启动已存在容器
docker stop redis     # 停止容器

# Git 操作
git status            # 查看状态
git diff              # 查看变更
git log --oneline -10 # 查看最近提交
```

## Code Style

- TypeScript strict mode, 2 spaces indent
- Single quotes `'`, semicolons required
- camelCase for variables/functions, PascalCase for classes/interfaces
- kebab-case for filenames (e.g., `reminder-queue.ts`)
- Comments: 英文为主，中文补充

## Key Directories

- `src/queues/` - BullMQ 队列定义
- `src/workers/` - Worker 处理器
- `src/jobs/` - 任务类型定义
- `src/utils/` - 工具函数
- `tests/` - Vitest 测试文件

## Workflow

### 修改代码前
1. 先阅读相关文件理解现有结构
2. 运行 `pnpm test` 确保测试通过
3. 如果是复杂任务，先制定计划再编码

### 修改代码后
1. 运行 `pnpm test` 验证变更
2. 运行 `pnpm lint` 检查代码风格
3. 同步更新或添加相关测试

### Git 提交格式
```
<emoji> <type>(<scope>): <subject>

WHAT: <动作与对象>
WHY: <业务目标或缺陷背景>
HOW: <实现策略>
```

Type 映射: feat=✨, fix=🐛, docs=📝, style=🎨, refactor=♻️, perf=⚡️, test=✅, build=🏗️, ci=🤖, chore=🧹

## IMPORTANT

- **使用 pnpm**，不要用 npm 或 yarn
- **Python 环境使用 UV** 管理虚拟环境
- **Redis 使用 Docker** 部署
- 修改代码后必须运行测试验证
- 提交前确保 lint 和 test 都通过

## Testing

- 框架：Vitest
- 运行单个测试：`pnpm vitest run -t "<test name>"`
- 测试文件放在 `tests/` 目录
- 修改代码时同步更新测试

## Project Context

这是一个基于 BullMQ 的定时任务服务，为语音聊天助手提供定时能力。
核心功能：定时提醒、周期任务、延迟执行、任务监控。

## LLM API (测试用)

| 用途 | 模型 | Host |
|------|------|------|
| LLM | glm-4-flash-250414 | https://open.bigmodel.cn/api/paas/v4 |
| Embedding | bge-m3 | https://ai.gitee.com/v1 |
| Rerank | bge-reranker-v2-m3 | https://ai.gitee.com/v1/rerank |
