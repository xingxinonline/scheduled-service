# Scheduled Service

基于 BullMQ 的定时任务服务，为语音聊天助手提供可靠的定时任务调度能力。

## 功能特性

- ⏰ 定时提醒与通知
- 🔄 周期性任务执行
- ⏳ 延迟任务处理
- 📊 任务状态管理与监控

## 技术栈

- **Runtime**: Node.js
- **Queue System**: BullMQ (基于 Redis)
- **Language**: TypeScript
- **Test Framework**: Vitest

## 快速开始

### 环境准备

1. 安装依赖
```bash
pnpm install
```

2. 启动 Redis（使用 Docker）
```bash
docker run -d --name redis -p 6379:6379 redis:latest
```

3. 配置环境变量
```bash
cp .env.example .env
# 编辑 .env 文件配置相关参数
```

### 开发

```bash
# 启动开发服务器
pnpm dev

# 运行测试
pnpm test

# 构建生产版本
pnpm build
```

## 项目结构

```
scheduled-service/
├── src/                    # 源代码目录
│   ├── queues/            # BullMQ 队列定义
│   ├── workers/           # Worker 处理器
│   ├── jobs/              # 任务定义
│   ├── utils/             # 工具函数
│   └── index.ts           # 入口文件
├── tests/                  # 测试文件
├── AGENTS.md              # Agent 开发指南
└── README.md              # 项目说明
```

## 相关文档

- [AGENTS.md](./AGENTS.md) - Agent 开发指南与项目规范
- [BullMQ 官方文档](https://docs.bullmq.io/)

## License

MIT
