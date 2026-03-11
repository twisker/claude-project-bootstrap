# CLAUDE.md

本文件为 Claude Code 提供项目工作指引。

## 项目简介

**{项目名称}** -- {一句话描述}

- 项目代号：`{代号}`
- 版本：v0.1.0
- 当前阶段：{当前阶段}

## 核心文档索引

| 文档 | 说明 |
|------|------|
| `.claude/COLLABORATION.md` | 协作框架：角色分工、工作步骤、代码提交原则 |
| `.claude/tech-spec-registry.md` | 技术规格：技术栈、配色体系、布局规范、API依赖 |
| `.claude/arch-spec-registry.md` | 架构规格：环境规划、部署、CI/CD、可观测性 |
| `.claude/sprint-plan.md` | 迭代计划：各阶段的任务拆分与进度 |
| `.claude/module-spec-registry.md` | 模块索引：各模块的设计文档与源码位置 |
| `.claude/test-registry.md` | 测试登记：各模块测试覆盖与测试场景 |
| `.claude/validation-registry.md` | 验收标准：通用交付标准与模块专项验收标准 |
| `.claude/current-sprint.md` | 当前Sprint：实时任务状态、活跃文件、改动记录 |
| `人工TODO事项.md` | 人工事项跟踪：需要人工介入的待办与已完成事项 |

---

## CLAUDE CODING 规范（必须遵守）

### Git 提交纪律（最高优先级）

**每次完成一组有意义的改动后，必须立即执行 `git add` + `git commit`，不得拖延、不得遗忘、不得等用户提醒。**

具体规则：
1. 产生了新文件或修改了现有文件，只要具有永久保存价值，就必须 `git add` 纳入版本管理
2. 每完成一个逻辑上完整的改动，立即 `git commit`，附带简明扼要的提交说明
3. 如果一次工作涉及多个不相关的改动，按逻辑分批提交
4. `git push` **禁止自动执行**，必须由人工手动触发
5. `.idea/`、`node_modules/`、`__pycache__/` 等 IDE/工具目录不纳入版本管理

### 工作原则

* 整个项目工作过程严格遵循 `.claude/COLLABORATION.md` 中指定的工作框架。

### 项目开始时

* 根据需求文档，确定开发技术框架，完善 `.claude/tech-spec-registry.md`
* 制定工作计划，完善 `.claude/sprint-plan.md`
* 完善 `.claude/module-spec-registry.md`
* 完善 `.claude/test-registry.md` 以及 `.claude/validation-registry.md`
* 依照工作计划，依次开展每个 Sprint 的开发

### 每个 Sprint 开始时

* 由人工确认是否开始此 Sprint，在收到明确答复后才开始开发
* 根据 `.claude/sprint-plan.md` 以及当前项目进度，更新 `.claude/current-sprint.md`
* 检查 `人工TODO事项.md` 中本 Sprint 相关的人工前置事项是否已完成
* 依照此 Sprint 的任务列表，依次开展每个任务的开发

### 每个任务开始时

* 由人工确认是否开始此项任务
* 读取 `.claude/tech-spec-registry.md`、`.claude/module-spec-registry.md`、`.claude/sprint-plan.md`、`.claude/current-sprint.md`、`.claude/test-registry.md`
* 明确技术规格、模块边界、项目进度、测试要求

### 每个任务完成前

* 读取 `.claude/test-registry.md`，依次进行测试
* 读取 `.claude/validation-registry.md`，对照自检

### 每个任务完成后

* 更新 `.claude/current-sprint.md`
* 更新 `.claude/module-spec-registry.md`
* 把测试结果回写到 `.claude/test-registry.md`

### 每个 Sprint 完成后

* 把 `.claude/current-sprint.md` 内容复制到 `.claude/archive` 目录下存档
* 更新 `.claude/sprint-plan.md`
* 同步更新 `人工TODO事项.md`
* 清空 `.claude/current-sprint.md` 内容

---

## 关键约束

{根据项目特定约束填充}

## 任务拆分步骤

对于每个阶段的任务，按以下步骤进行：

1. 先形成设计稿/原型
2. 所有设计经人工确认后方可进入开发
3. 分解出前端开发任务
4. 分解出后端 API 开发任务
5. 分解出框架性后端开发任务（权限、数据等）
6. 分解出测试场景及测试用例
7. 分解出 CI/CD 相关任务
8. 分解出部署相关任务
9. 按优先级安排：先撰写测试 -> 框架 API -> 后端 API -> 前端页面 -> CI/CD -> 部署
