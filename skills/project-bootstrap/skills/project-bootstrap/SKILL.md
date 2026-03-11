---
name: project-bootstrap
description: "Bootstrap any new project with a production-grade AI-Human collaboration framework. Generates README.md, CLAUDE.md, .claude/ registry files, git version hooks, and sprint planning templates. Use this skill whenever the user wants to start a new project, initialize a project, set up project scaffolding, create project structure, or mentions needing a development framework — even if they don't explicitly say 'bootstrap'. Also applies when the user has a PRD or requirements doc and wants to turn it into a working project setup."
user-invocable: true
argument-hint: "[path-to-prd-or-description]"
---

# Project Bootstrap

为任意新项目生成完整的 AI-Human 协作开发框架。通过对话理解用户意图，最终产出 README.md、CLAUDE.md 和 .claude/ 目录下的全套协作规范文件，并配置 git 版本自动管理钩子。

**用户输入：** $ARGUMENTS

---

## Phase 1: 需求采集（对话式）

**目标：** 通过对话充分理解项目的真实意图和边界。根据用户已提供的信息量灵活调整——信息充足时可以快速确认，信息不足时多问几轮。避免重复询问用户已经说过的内容。

如果用户提供了文档路径（$ARGUMENTS），先读取文档内容，从中提取信息，然后向用户确认并补充遗漏项。如果用户提供了描述文字，从中提取信息后同样确认补充。如果什么都没提供，直接进入提问流程。

### 需要采集的信息

以下是需要了解的信息清单，根据已知信息灵活组织提问，一次可以问多个相关问题：

**基础信息：**
1. **项目名称与代号** — 项目的正式名称？英文代号？
2. **一句话描述** — 用一句话描述这个项目做什么？
3. **目标用户** — 谁会使用这个产品？
4. **核心问题** — 它解决什么问题？
5. **输入文档** — 是否有 PRD、需求文档、竞品分析等参考文档？

**技术方向：**
6. **技术栈偏好** — 前端框架？后端语言/框架？数据库？已定技术选型？
7. **部署环境** — 云平台？容器化？Serverless？本地？
8. **团队规模** — 几个人开发？AI 占多大比重？
9. **项目规模预估** — 大概多少个页面/API/模块？分几个阶段？

**协作模式：**
10. **角色分工** — 人工负责什么？AI 负责什么？审批流程？
11. **关键约束** — 时间限制？合规要求？性能指标？
12. **特殊要求** — 行业术语？文档语言偏好？

### 确认

将收集到的信息整理成简要摘要，展示给用户确认。用户可修正或补充。

**进入 Phase 2 的条件：** 项目名称 + 一句话描述 + 至少一个技术栈方向 + 角色分工明确 + 交付阶段有初步规划。

---

## Phase 2: 文件生成

按以下顺序生成文件。全部生成完毕后统一 `git add` + `git commit`。

每个文件的内容必须根据 Phase 1 采集的信息**完整填充**，所有 `{占位符}` 都必须替换为实际内容，不得原样保留。如果某个章节不适用于当前项目，标注"不适用"而非删除。

### 生成文件清单

按以下顺序逐一生成。生成每个文件前，先读取对应的 reference 模板文件，理解其结构和意图，然后结合 Phase 1 采集的信息填充内容：

1. **`.claude/COLLABORATION.md`** — 读取 `references/collaboration-template.md`，生成协作规范
2. **`.claude/tech-spec-registry.md`** — 读取 `references/tech-spec-template.md`，生成技术规格登记
3. **`.claude/arch-spec-registry.md`** — 读取 `references/arch-spec-template.md`，生成架构规格登记
4. **`.claude/sprint-plan.md`** — 读取 `references/sprint-plan-template.md`，生成迭代计划
5. **`.claude/current-sprint.md`** — 读取 `references/current-sprint-template.md`，生成当前迭代状态
6. **`.claude/module-spec-registry.md`** — 读取 `references/module-spec-template.md`，生成模块索引
7. **`.claude/test-registry.md`** — 读取 `references/test-registry-template.md`，生成测试登记
8. **`.claude/validation-registry.md`** — 读取 `references/validation-template.md`，生成验收标准
9. **`.claude/archive/`** — 创建空目录（加一个 `.gitkeep`）
10. **`人工TODO事项.md`** — 读取 `references/human-todo-template.md`，生成人工待办清单
11. **`README.md`** — 面向人类阅读：项目名称、架构、模块、技术栈、目录结构、路线图。**不放 AI 协作内部细节。**
12. **`CLAUDE.md`** — 读取 `references/claude-md-template.md`，生成项目指令文件。**精简扼要，细节全部引用 `.claude/` 子文件。**

---

## Phase 3: 版本管理钩子

生成以下文件以实现语义化版本自动管理：

先读取 `references/scripts-template.md`，理解各脚本的实现逻辑，然后生成：

1. **`VERSION`** — 内容为 `0.1.0`
2. **`scripts/bump-patch.sh`** — 自动递增 patch（git hook 调用）
3. **`scripts/bump-minor.sh`** — 递增 minor + reset patch（人工调用）
4. **`scripts/bump-major.sh`** — 递增 major + reset minor/patch（人工调用）
5. **`.githooks/pre-commit`** — 在有非 VERSION 文件变更时自动调用 bump-patch
6. 执行 `chmod +x` 赋权 + `git config core.hooksPath .githooks` 激活钩子
7. **`.gitignore`** — 如不存在则创建，存在则追加缺失项（.idea/, node_modules/, __pycache__/, .env 等）

---

## Phase 4: 提交与总结

1. `git add` 所有生成的文件
2. `git commit -m "chore: 初始化项目协作框架（README + CLAUDE.md + .claude/ 规范 + 版本管理钩子）"`
3. 向用户展示生成的文件清单树
4. 提醒用户：
   - 检查并调整生成的文件内容
   - `git push` 需人工执行
   - 后续可修改 `.claude/` 下的文件来演进规范
   - 版本钩子已激活，每次 commit 会自动递增 patch 版本

---

## 核心设计原则

1. **CLAUDE.md 精简扼要** — 只放索引和最关键的规则，细节全部引用 `.claude/` 子文件
2. **细节分门别类** — 技术规格、架构规格、模块索引、测试登记、验收标准各司其职
3. **生产级开发逻辑** — 先计划后执行、多次迭代、小步快跑、测试驱动、CI/CD、环境分离、灰度发布
4. **文档随迭代进化** — current-sprint 实时更新 + archive 存档留痕 + sprint-plan 持续演进
5. **Git 有机结合** — 自动 add/commit、pre-commit hook 自动 patch 版本、push 人工控制
6. **版本语义化** — major.minor.patch 格式，patch 自动递增，major/minor 人工触发脚本
