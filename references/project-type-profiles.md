# 项目类型画像

每种项目类型的默认配置，用于决定问哪些问题、跳过哪些问题。

## 全栈 Web 应用

- **架构**：全栈框架一体化 或 前后端分离
- **必须问**：语言、前端框架、后端框架（或全栈框架）、数据库、ORM、CSS/UI 方案
- **按需问**：认证、部署、CI/CD、测试
- **典型目录结构**（以 Next.js 为例）：
  ```
  project/
  ├── app/              # 路由和页面
  ├── components/       # UI 组件
  ├── lib/              # 工具函数
  ├── prisma/           # 数据库 schema
  ├── public/           # 静态资源
  └── tests/            # 测试
  ```

## 纯后端 API

- **架构**：单体应用
- **必须问**：语言、后端框架、数据库、ORM、API 风格（REST/GraphQL）
- **跳过**：前端框架、CSS/UI 方案
- **按需问**：认证、部署、容器化
- **典型目录结构**（以 FastAPI 为例）：
  ```
  project/
  ├── app/
  │   ├── api/          # 路由
  │   ├── models/       # 数据模型
  │   ├── schemas/      # Pydantic schema
  │   ├── services/     # 业务逻辑
  │   └── core/         # 配置、依赖
  ├── tests/
  ├── alembic/          # 数据库迁移
  └── requirements.txt
  ```

## 纯前端

- **架构**：SPA
- **必须问**：前端框架、CSS/UI 方案、状态管理
- **跳过**：后端框架、数据库、ORM
- **按需问**：部署、测试
- **典型目录结构**（以 React 为例）：
  ```
  project/
  ├── src/
  │   ├── components/   # UI 组件
  │   ├── pages/        # 页面
  │   ├── hooks/        # 自定义 Hook
  │   ├── store/        # 状态管理
  │   └── utils/        # 工具函数
  ├── public/
  └── tests/
  ```

## CLI 工具

- **架构**：命令行入口 + 子命令
- **必须问**：语言、CLI 框架、分发方式
- **跳过**：前端、数据库、CSS/UI、部署
- **典型目录结构**（以 Go + Cobra 为例）：
  ```
  project/
  ├── cmd/              # 子命令定义
  │   └── root.go
  ├── internal/         # 内部逻辑
  ├── pkg/              # 可导出的包
  └── main.go
  ```

## 移动端应用

- **架构**：客户端 + 可选后端
- **必须问**：原生/跨平台、框架
- **按需问**：是否需要后端（需要则走后端 API 流程）
- **典型目录结构**（以 React Native 为例）：
  ```
  project/
  ├── src/
  │   ├── screens/      # 页面
  │   ├── components/   # 组件
  │   ├── navigation/   # 导航
  │   └── services/     # API 调用
  ├── android/
  ├── ios/
  └── __tests__/
  ```

## 库/SDK

- **架构**：模块化包
- **必须问**：语言、构建工具、发布方式（npm/PyPI/crates.io）
- **跳过**：前端框架、数据库、部署
- **按需问**：文档方案、测试框架
- **典型目录结构**：
  ```
  project/
  ├── src/              # 源代码
  ├── tests/            # 测试
  ├── docs/             # 文档
  ├── examples/         # 使用示例
  └── README.md
  ```

## 数据处理管线

- **架构**：ETL Pipeline
- **必须问**：语言、数据存储、调度方式
- **跳过**：前端框架、CSS/UI
- **按需问**：容器化、监控
- **典型目录结构**（以 Python 为例）：
  ```
  project/
  ├── pipelines/        # 管线定义
  ├── transforms/       # 数据转换逻辑
  ├── schemas/          # 数据 schema
  ├── tests/
  └── config/           # 配置文件
  ```
