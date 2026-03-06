# claude-project-bootstrap

一个 Claude Code 插件，为任意新项目一键生成**生产级 AI-人工协作开发框架**。

## 它做了什么

通过 3~5 轮交互式对话，理解你的项目意图，然后自动生成一整套协作规范文件：

### 生成的文件清单

| 文件 | 用途 |
|------|------|
| `README.md` | 面向人类的项目说明 |
| `CLAUDE.md` | AI Agent 工作指引（精简索引式，不堆砌细节） |
| `.claude/COLLABORATION.md` | 协作框架：角色分工、工作步骤、代码提交原则 |
| `.claude/tech-spec-registry.md` | 技术规格：技术栈、配色体系、布局规范、API 依赖 |
| `.claude/arch-spec-registry.md` | 架构规格：环境规划、CI/CD 流水线、可观测性、灰度发布 |
| `.claude/sprint-plan.md` | 迭代计划：Phase/Sprint 任务拆分、优先级、责任人 |
| `.claude/current-sprint.md` | 当前 Sprint 实时状态：任务进度、活跃文件、改动记录 |
| `.claude/module-spec-registry.md` | 模块索引：各模块的设计文档与源码位置 |
| `.claude/test-registry.md` | 测试登记：覆盖率要求、测试场景、测试结果 |
| `.claude/validation-registry.md` | 验收标准：通用交付标准 + 模块专项验收条件 |
| `.claude/archive/` | Sprint 存档目录（留痕审计） |
| `人工TODO事项.md` | 人工待办跟踪（与 sprint-plan 双向同步） |
| `VERSION` | 语义化版本文件（`major.minor.patch`） |
| `.githooks/pre-commit` | Git 钩子：每次 commit 自动递增 patch 版本 |
| `scripts/bump-*.sh` | 人工调用的 major/minor 版本升级脚本 |

## 设计理念

### 1. CLAUDE.md 精简扼要

CLAUDE.md 只存放文档索引和最关键的规则。所有技术细节、流程约定、验收标准都分门别类放在 `.claude/` 目录下的专属文件中。这样做的好处：

- AI Agent 启动时快速获取全局视图，不被细节淹没
- 每个注册表文件可以独立演进，不会让 CLAUDE.md 越改越臃肿
- 人工审查时也能快速定位关注的领域

### 2. 细节分门别类

| 注册表文件 | 关注的问题 |
|-----------|-----------|
| `tech-spec-registry` | 用什么技术？什么规范？ |
| `arch-spec-registry` | 怎么部署？怎么发布？ |
| `sprint-plan` | 做什么？按什么顺序做？ |
| `module-spec-registry` | 代码在哪里？文档在哪里？ |
| `test-registry` | 怎么测试？覆盖了多少？ |
| `validation-registry` | 交付标准是什么？ |

### 3. 生产级开发逻辑

遵循大型生产项目的普遍开发节奏：

```
计划 -> 设计 -> 测试先行 -> 开发 -> 联调 -> CI/CD -> 灰度 -> 生产
```

- **先计划后执行**：每个 Sprint 有明确目标和任务拆分
- **小步快跑**：按 Sprint 迭代，每个 Sprint 产出可验证的交付物
- **测试驱动**：先写测试用例，再实现功能
- **环境分离**：local / staging / gray / production 四级环境
- **灰度发布**：先小流量验证，再全量上线

### 4. 文档随迭代进化

- `current-sprint.md` 实时记录当前工作状态
- 每个 Sprint 结束后，`current-sprint.md` 归档到 `.claude/archive/`
- `sprint-plan.md` 根据实际进度持续更新
- 所有变更都有据可查

### 5. 与 Git 有机结合

- AI 完成改动后自动 `git add` + `git commit`
- `pre-commit` 钩子自动递增 patch 版本号
- `git push` 始终由人工手动触发（安全守门）
- 提供 `bump-minor.sh` 和 `bump-major.sh` 供人工调用

### 6. 语义化版本管理

版本格式：`major.minor.patch`（如 `1.2.34`）

| 版本段 | 更新方式 | 触发条件 |
|--------|---------|---------|
| patch | 自动 | 每次 `git commit` 时由 pre-commit 钩子触发 |
| minor | 人工 | 运行 `scripts/bump-minor.sh` |
| major | 人工 | 运行 `scripts/bump-major.sh` |

---

## 安装

### 方式一：Plugin 命令安装（推荐）

在 Claude Code 会话中执行：

```
/plugin marketplace add twisker/claude-project-bootstrap
/plugin install project-bootstrap
```

### 方式二：编辑 settings.json

在 `~/.claude/settings.json` 中添加：

```json
{
  "extraKnownMarketplaces": {
    "claude-project-bootstrap": {
      "source": {
        "source": "github",
        "repo": "twisker/claude-project-bootstrap"
      }
    }
  }
}
```

然后在 Claude Code 中执行：

```
/plugin install project-bootstrap
```

### 方式三：本地安装（开发/测试用）

```bash
git clone https://github.com/twisker/claude-project-bootstrap.git
cd your-project
claude --plugin-dir /path/to/claude-project-bootstrap
```

### 方式四：复制到项目内（作为项目级 Skill）

```bash
mkdir -p .claude/skills/project-bootstrap
cp -r skills/project-bootstrap/* .claude/skills/project-bootstrap/
```

这种方式会让 Skill 成为项目的一部分，适合团队内部统一规范。

---

## 使用方法

安装完成后，在任意 Claude Code 会话中：

### 交互式对话（无参数）

```
/project-bootstrap
```

Claude 会通过 3~5 轮提问引导你描述项目，然后生成全部文件。

### 从文档生成

```
/project-bootstrap ./docs/PRD.md
```

Claude 会先读取文档提取信息，再向你确认补充。

### 从描述文字生成

```
/project-bootstrap "一个面向电商商家的客服绩效分析 SaaS 平台"
```

---

## 执行流程

```
Phase 1: 需求采集        Phase 2: 文件生成         Phase 3: 版本钩子        Phase 4: 提交
┌──────────────┐     ┌──────────────────┐     ┌────────────────┐     ┌──────────┐
│ 第1轮 基础信息 │     │ COLLABORATION.md │     │ VERSION        │     │ git add  │
│ 第2轮 技术方向 │ --> │ tech-spec-reg.   │ --> │ bump-patch.sh  │ --> │ git commit│
│ 第3轮 协作模式 │     │ arch-spec-reg.   │     │ bump-minor.sh  │     │ 展示清单  │
│ 第4轮 确认摘要 │     │ sprint-plan.md   │     │ bump-major.sh  │     │ 提醒push │
│              │     │ ... (共12个文件)   │     │ pre-commit hook│     │          │
└──────────────┘     └──────────────────┘     └────────────────┘     └──────────┘
```

### Phase 1 采集的信息

| 轮次 | 采集内容 |
|------|---------|
| 第 1 轮 | 项目名称、代号、一句话描述、目标用户、核心问题、参考文档 |
| 第 2 轮 | 技术栈偏好、部署环境、团队规模、项目规模预估 |
| 第 3 轮 | 角色分工、关键约束、特殊要求 |
| 第 4 轮 | 信息确认与补充 |

---

## 插件目录结构

```
claude-project-bootstrap/
├── .claude-plugin/
│   ├── plugin.json                 # 插件清单（名称、版本、作者）
│   └── marketplace.json            # Marketplace 目录（供他人发现和安装）
├── skills/
│   └── project-bootstrap/
│       ├── SKILL.md                # Skill 主文件（YAML 元数据 + 执行指令）
│       └── references/             # 模板文件（Claude 按模板填充生成）
│           ├── collaboration-template.md    # 协作框架模板
│           ├── tech-spec-template.md        # 技术规格模板
│           ├── arch-spec-template.md        # 架构规格模板
│           ├── sprint-plan-template.md      # 迭代计划模板
│           ├── current-sprint-template.md   # 当前Sprint模板
│           ├── module-spec-template.md      # 模块索引模板
│           ├── test-registry-template.md    # 测试登记模板
│           ├── validation-template.md       # 验收标准模板
│           ├── human-todo-template.md       # 人工待办模板
│           ├── claude-md-template.md        # CLAUDE.md 模板
│           └── scripts-template.md          # 版本管理脚本模板
├── README.md              # 英文说明
├── README.zh-CN.md        # 中文说明（本文件）
└── LICENSE                # MIT 许可证
```

---

## 适用场景

- 从零开始的新项目，需要快速建立 AI 协作规范
- 团队希望统一 Claude Code 的使用方式和文档结构
- 需要生产级的迭代管理、测试驱动、CI/CD 流程模板
- 希望 AI 和人工各司其职，人工保留决策权，AI 负责执行

## 语言

默认输出语言为**中文**。Claude 会根据对话中的语言自动适配——如果你用英文交流，生成的文件也会是英文。

## 许可证

MIT
