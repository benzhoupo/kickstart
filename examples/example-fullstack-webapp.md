# TaskFlow -- 项目规格说明书

> 生成时间：2026-03-29

## 1. 项目概览

| 字段 | 内容 |
|------|------|
| 项目名称 | TaskFlow |
| 一句话描述 | 团队任务管理和协作平台 |
| 项目类型 | 全栈 Web 应用 |
| 目标用户 | 面向用户的产品 |

### 核心功能

- 任务创建、分配、跟踪
- 看板视图（Kanban）
- 实时协作和通知
- 团队成员管理
- 数据统计仪表盘

## 2. 技术栈

| 类别 | 选择 | 说明 |
|------|------|------|
| 编程语言 | TypeScript | 全栈统一语言，类型安全 |
| 全栈框架 | Next.js 14 (App Router) | SSR/SSG 支持，API Routes 内置 |
| 数据库 | PostgreSQL | 关系型数据，复杂查询支持好 |
| ORM | Prisma | 类型安全，schema 优先 |
| CSS/UI 方案 | Tailwind CSS + shadcn/ui | 原子化 CSS + 可复制组件 |
| 包管理器 | pnpm | 快速，磁盘占用小 |
| 实时通信 | Socket.io | WebSocket 封装，断线重连 |
| 认证 | NextAuth.js | 多 Provider 支持 |

## 3. 系统架构

### 架构模式

全栈框架一体化：Next.js App Router 同时处理前端渲染和 API 路由，共享 TypeScript 类型定义。实时通知通过独立的 Socket.io 服务处理。

### 核心模块

- **认证模块** -- 用户注册、登录、权限管理
- **任务模块** -- CRUD、状态流转、分配
- **看板模块** -- 拖拽排序、列管理
- **通知模块** -- 实时推送、邮件通知
- **统计模块** -- 任务完成率、团队效率

### 数据流

用户操作 → Next.js Server Action / API Route → Prisma → PostgreSQL
实时更新 → Socket.io Server → 广播到相关用户客户端

## 4. 目录结构

```
taskflow/
├── app/                    # Next.js App Router
│   ├── (auth)/             # 认证相关页面
│   │   ├── login/
│   │   └── register/
│   ├── (dashboard)/        # 主应用页面
│   │   ├── board/          # 看板视图
│   │   ├── tasks/          # 任务列表
│   │   └── settings/       # 设置
│   ├── api/                # API 路由
│   │   ├── auth/
│   │   ├── tasks/
│   │   └── teams/
│   ├── layout.tsx
│   └── page.tsx
├── components/             # UI 组件
│   ├── ui/                 # shadcn/ui 组件
│   ├── task/               # 任务相关组件
│   ├── board/              # 看板相关组件
│   └── layout/             # 布局组件
├── lib/                    # 工具函数
│   ├── auth.ts
│   ├── db.ts
│   └── utils.ts
├── prisma/                 # 数据库
│   ├── schema.prisma
│   └── migrations/
├── server/                 # Socket.io 服务
│   └── socket.ts
├── public/                 # 静态资源
├── tests/                  # 测试
├── .env.example
├── next.config.js
├── tailwind.config.ts
├── tsconfig.json
└── package.json
```

## 5. 核心依赖

### 生产依赖

| 包名 | 用途 |
|------|------|
| next | 全栈框架 |
| react / react-dom | UI 库 |
| @prisma/client | 数据库客户端 |
| next-auth | 认证 |
| socket.io / socket.io-client | 实时通信 |
| tailwindcss | CSS 框架 |
| @hello-pangea/dnd | 拖拽排序 |
| zod | 数据验证 |
| date-fns | 日期处理 |

### 开发依赖

| 包名 | 用途 |
|------|------|
| typescript | 类型系统 |
| prisma | ORM CLI |
| eslint | 代码检查 |
| vitest | 单元测试 |
| @testing-library/react | 组件测试 |

## 6. 开发环境搭建

### 前置要求

- Node.js >= 18
- pnpm >= 8
- PostgreSQL >= 15（或使用 Docker）

### 初始化步骤

```bash
# 1. 创建项目
pnpm create next-app taskflow --typescript --tailwind --app --src-dir=false

# 2. 安装依赖
cd taskflow
pnpm add @prisma/client next-auth socket.io socket.io-client @hello-pangea/dnd zod date-fns
pnpm add -D prisma vitest @testing-library/react

# 3. 初始化 Prisma
pnpm prisma init

# 4. 配置环境变量
cp .env.example .env
# 编辑 .env 设置 DATABASE_URL 和 NEXTAUTH_SECRET

# 5. 数据库迁移
pnpm prisma migrate dev --name init
```

### 启动开发服务器

```bash
pnpm dev
```

## 7. 数据模型

### 核心实体

**User**
| 字段 | 类型 | 说明 |
|------|------|------|
| id | String (cuid) | 主键 |
| name | String | 用户名 |
| email | String (unique) | 邮箱 |
| password | String | 哈希密码 |
| avatar | String? | 头像 URL |
| teamId | String? | 所属团队 |

**Task**
| 字段 | 类型 | 说明 |
|------|------|------|
| id | String (cuid) | 主键 |
| title | String | 任务标题 |
| description | String? | 任务描述 |
| status | Enum | TODO/IN_PROGRESS/DONE |
| priority | Enum | LOW/MEDIUM/HIGH |
| assigneeId | String? | 负责人 |
| boardId | String | 所属看板 |
| order | Int | 排序位置 |
| dueDate | DateTime? | 截止日期 |

**Board**
| 字段 | 类型 | 说明 |
|------|------|------|
| id | String (cuid) | 主键 |
| name | String | 看板名称 |
| teamId | String | 所属团队 |
| columns | Json | 列定义 |

**Team**
| 字段 | 类型 | 说明 |
|------|------|------|
| id | String (cuid) | 主键 |
| name | String | 团队名称 |
| ownerId | String | 创建者 |

### 实体关系

- User 属于一个 Team（多对一）
- Task 属于一个 Board（多对一）
- Task 可以有一个 Assignee（User，多对一）
- Board 属于一个 Team（多对一）
- Team 有一个 Owner（User，一对一）

## 8. 实施计划

### Phase 1: 基础搭建

- [ ] 初始化 Next.js 项目
- [ ] 配置 Prisma + PostgreSQL
- [ ] 搭建 NextAuth 认证（邮箱密码）
- [ ] 实现基础布局（侧边栏、顶栏）

### Phase 2: 核心功能

- [ ] 任务 CRUD（创建、编辑、删除）
- [ ] 看板视图 + 拖拽排序
- [ ] 任务分配和状态流转
- [ ] 团队创建和成员邀请

### Phase 3: 完善和优化

- [ ] 实时通知（Socket.io）
- [ ] 数据统计仪表盘
- [ ] 搜索和过滤
- [ ] 响应式移动端适配

### Phase 4: 部署上线

- [ ] Vercel 部署配置
- [ ] PostgreSQL 云数据库（Supabase/Neon）
- [ ] 环境变量和安全配置
- [ ] 上线验证和监控

---

> 本文档由 kickstart skill 生成，可根据实际情况调整。
