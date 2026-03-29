# DataPipe -- 项目规格说明书

> 生成时间：2026-03-29

## 1. 项目概览

| 字段 | 内容 |
|------|------|
| 项目名称 | DataPipe |
| 一句话描述 | 内部数据分析 API 服务 |
| 项目类型 | 纯后端 API |
| 目标用户 | 团队内部 |

### 核心功能

- 用户行为数据聚合查询
- 报表数据 API
- 定时数据同步任务
- 数据导出（CSV/Excel）

## 2. 技术栈

| 类别 | 选择 | 说明 |
|------|------|------|
| 编程语言 | Python 3.12 | 数据处理生态丰富 |
| 后端框架 | FastAPI | 异步支持，自动生成 API 文档 |
| 数据库 | PostgreSQL | 复杂查询和聚合支持好 |
| ORM | SQLAlchemy 2.0 | 灵活，支持异步 |
| API 风格 | RESTful | 简单直观 |
| 包管理器 | uv | 快速的 Python 包管理器 |

## 3. 系统架构

### 架构模式

单体应用：所有 API 和定时任务在同一服务中，通过模块化组织代码。数据量增长后可拆分为独立的查询服务和同步服务。

### 核心模块

- **API 模块** -- RESTful 接口，处理查询请求
- **数据聚合模块** -- 复杂 SQL 查询和数据预处理
- **同步模块** -- 定时从外部数据源拉取数据
- **导出模块** -- CSV/Excel 文件生成和下载

### 数据流

外部数据源 → 定时同步任务 → PostgreSQL → API 查询 → JSON 响应
导出请求 → 查询数据 → 生成文件 → 返回下载链接

## 4. 目录结构

```
datapipe/
├── app/
│   ├── api/                # API 路由
│   │   ├── __init__.py
│   │   ├── reports.py      # 报表相关接口
│   │   ├── exports.py      # 导出相关接口
│   │   └── health.py       # 健康检查
│   ├── models/             # SQLAlchemy 模型
│   │   ├── __init__.py
│   │   └── analytics.py
│   ├── schemas/            # Pydantic 请求/响应模型
│   │   ├── __init__.py
│   │   └── report.py
│   ├── services/           # 业务逻辑
│   │   ├── __init__.py
│   │   ├── aggregation.py  # 数据聚合
│   │   ├── sync.py         # 数据同步
│   │   └── export.py       # 文件导出
│   ├── core/               # 核心配置
│   │   ├── config.py       # 配置管理
│   │   ├── database.py     # 数据库连接
│   │   └── deps.py         # 依赖注入
│   ├── tasks/              # 定时任务
│   │   └── sync_job.py
│   └── main.py             # 应用入口
├── alembic/                # 数据库迁移
│   ├── versions/
│   └── env.py
├── tests/
│   ├── test_api/
│   └── test_services/
├── alembic.ini
├── pyproject.toml
├── .env.example
└── Dockerfile
```

## 5. 核心依赖

### 生产依赖

| 包名 | 用途 |
|------|------|
| fastapi | Web 框架 |
| uvicorn | ASGI 服务器 |
| sqlalchemy[asyncio] | ORM（异步） |
| asyncpg | PostgreSQL 异步驱动 |
| alembic | 数据库迁移 |
| pydantic-settings | 配置管理 |
| openpyxl | Excel 文件生成 |
| apscheduler | 定时任务调度 |
| httpx | 异步 HTTP 客户端（数据同步用） |

### 开发依赖

| 包名 | 用途 |
|------|------|
| pytest | 测试框架 |
| pytest-asyncio | 异步测试支持 |
| ruff | 代码检查和格式化 |
| mypy | 类型检查 |

## 6. 开发环境搭建

### 前置要求

- Python >= 3.12
- uv
- PostgreSQL >= 15（或使用 Docker）

### 初始化步骤

```bash
# 1. 创建项目
mkdir datapipe && cd datapipe
uv init

# 2. 安装依赖
uv add fastapi uvicorn "sqlalchemy[asyncio]" asyncpg alembic pydantic-settings openpyxl apscheduler httpx
uv add --dev pytest pytest-asyncio ruff mypy

# 3. 初始化 Alembic
alembic init alembic

# 4. 配置环境变量
cp .env.example .env
# 编辑 .env 设置 DATABASE_URL

# 5. 创建数据库
createdb datapipe

# 6. 运行迁移
alembic upgrade head
```

### 启动开发服务器

```bash
uv run uvicorn app.main:app --reload --port 8000
```

API 文档访问：http://localhost:8000/docs

## 7. 数据模型

### 核心实体

**UserEvent**（用户行为事件）
| 字段 | 类型 | 说明 |
|------|------|------|
| id | BigInteger | 主键，自增 |
| user_id | String | 用户标识 |
| event_type | String | 事件类型 |
| properties | JSONB | 事件属性 |
| timestamp | DateTime | 事件时间 |
| source | String | 数据来源 |

**Report**（报表缓存）
| 字段 | 类型 | 说明 |
|------|------|------|
| id | UUID | 主键 |
| name | String | 报表名称 |
| query_config | JSONB | 查询配置 |
| result_cache | JSONB | 缓存结果 |
| expires_at | DateTime | 缓存过期时间 |

**SyncJob**（同步任务记录）
| 字段 | 类型 | 说明 |
|------|------|------|
| id | UUID | 主键 |
| source | String | 数据源名称 |
| status | Enum | PENDING/RUNNING/SUCCESS/FAILED |
| started_at | DateTime | 开始时间 |
| finished_at | DateTime? | 完成时间 |
| records_synced | Integer | 同步记录数 |

### 实体关系

- UserEvent 是独立表，按 timestamp 分区查询
- Report 引用 UserEvent 进行聚合查询
- SyncJob 记录数据同步状态，与 UserEvent 通过 source 关联

## 8. 实施计划

### Phase 1: 基础搭建

- [ ] 初始化 FastAPI 项目结构
- [ ] 配置 SQLAlchemy + PostgreSQL 连接
- [ ] 配置 Alembic 迁移
- [ ] 实现健康检查接口

### Phase 2: 核心功能

- [ ] 数据聚合查询 API（按时间、用户、事件类型）
- [ ] 报表数据 API + 缓存机制
- [ ] 定时数据同步任务
- [ ] CSV/Excel 导出功能

### Phase 3: 完善和优化

- [ ] 查询性能优化（索引、分页）
- [ ] 错误处理和日志
- [ ] API 认证（JWT）
- [ ] 单元测试和集成测试

### Phase 4: 部署上线

- [ ] Dockerfile 编写
- [ ] Docker Compose 配置（API + PostgreSQL）
- [ ] 内网部署
- [ ] 接口文档分享给前端团队

---

> 本文档由 kickstart skill 生成，可根据实际情况调整。
