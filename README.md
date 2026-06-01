# kickstart

> **English speakers:** No English version available. Go find a fan translation somewhere. Not my problem if you can't.


从一个模糊的想法到一份可以直接开工的项目规格说明书。

## 这是什么

kickstart 是一个 Claude Code Skill。它通过交互式问答帮你规划新项目：问你要做什么、给谁用、技术栈怎么选、架构怎么定，最后生成一份完整的 Markdown 规格文件，保存到你的工作目录。

生成的规格文件包含：项目概览、技术栈决策（附理由）、系统架构、目录结构、核心依赖、开发环境搭建步骤、数据模型、分阶段实施计划。拿到手就能开始写代码。

## 特点

- **你做主** -- 所有技术决策（语言、框架、数据库、UI、部署）都列出选项让你自己选，不替你做决定
- **自适应问答** -- 后端项目不会问你 UI 框架，CLI 工具不会问你数据库。问该问的，跳不相关的
- **输出可执行** -- 具体到可以 `npm init` / `pip install` 的程度，不是空中楼阁

## 安装

复制整个目录到 Claude Code 的 skills 目录：

```bash
# 全局安装（所有项目可用）
cp -r kickstart/ ~/.claude/skills/kickstart/

# 或项目级安装（仅当前项目可用）
cp -r kickstart/ 你的项目/.claude/skills/kickstart/
```

也可以用 [cc-switch](https://github.com/farion1231/cc-switch) 管理 skills 的安装和切换。

## 触发方式

在 Claude Code 中说：

- "帮我新建一个项目"
- "我想做一个 XX"
- "项目规划" / "技术选型"
- "bootstrap" / "scaffold"
- "/kickstart"

## 文件结构

```
kickstart/
├── README.md                          ← 你在看的这个
├── SKILL.md                           ← 主文件
├── references/
│   ├── qa-flow.md                     ← 问答流程和分支逻辑
│   ├── tech-stack-catalog.md          ← 技术栈选项索引
│   ├── output-template.md            ← 输出文档模板
│   ├── architecture-patterns.md      ← 6 种架构模式
│   └── project-type-profiles.md      ← 7 种项目类型画像
└── examples/
    ├── example-fullstack-webapp.md    ← 示例：全栈 Web 应用
    └── example-backend-api.md         ← 示例：后端 API
```

## 输出示例

生成的规格文件大致长这样（节选）：

```markdown
# TaskFlow 项目规格说明书

## 1. 项目概览
- 项目名称：TaskFlow
- 一句话描述：团队任务管理平台
- 目标用户：10-50 人的技术团队
- 核心功能：任务看板、实时协作、权限管理

## 2. 技术栈
| 类别 | 选择 | 理由 |
|------|------|------|
| 框架 | Next.js 14 | 全栈一体，SSR 支持 |
| 数据库 | PostgreSQL | 关系型数据，事务支持 |
| ORM | Prisma | 类型安全，迁移方便 |
...
```

完整示例见 `examples/` 目录。

## 许可证

MIT
