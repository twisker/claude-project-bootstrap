# claude-project-bootstrap

A Claude Code plugin that bootstraps any new project with a production-grade AI-Human collaboration framework.

## What it does

Through 3~5 rounds of interactive dialogue, this skill understands your project intent and generates a complete set of collaboration files:

| Generated File | Purpose |
|----------------|---------|
| `README.md` | Human-readable project overview |
| `CLAUDE.md` | AI agent instructions (concise, index-based) |
| `.claude/COLLABORATION.md` | Role definitions, workflow steps, commit rules |
| `.claude/tech-spec-registry.md` | Tech stack, color scheme, layout, API dependencies |
| `.claude/arch-spec-registry.md` | Architecture, environments, CI/CD, observability |
| `.claude/sprint-plan.md` | Sprint/Phase task breakdown with priorities |
| `.claude/current-sprint.md` | Live sprint status, active files, change log |
| `.claude/module-spec-registry.md` | Module index: design docs + source locations |
| `.claude/test-registry.md` | Test coverage tracking per module |
| `.claude/validation-registry.md` | Acceptance criteria: universal + module-specific |
| `.claude/archive/` | Sprint archive directory for audit trail |
| `VERSION` | Semantic version file (`major.minor.patch`) |
| `.githooks/pre-commit` | Auto-increment patch version on commit |
| `scripts/bump-*.sh` | Manual major/minor version bump scripts |

## Design Principles

1. **CLAUDE.md stays lean** -- index + critical rules only; details live in `.claude/` sub-files
2. **Separation of concerns** -- tech specs, architecture, modules, tests, validation each in their own registry
3. **Production-grade workflow** -- plan first, execute in sprints, test-driven, CI/CD, staging/gray/prod separation
4. **Living documentation** -- current-sprint updates in real-time, archives preserve history
5. **Git-native** -- auto add/commit, pre-commit hook for patch version, push stays manual
6. **Semantic versioning** -- `major.minor.patch`; patch auto-increments, major/minor are human-triggered

## Installation

### Option 1: Plugin install (recommended)

```bash
# Add the marketplace
/plugin marketplace add twisker/claude-project-bootstrap

# Install the plugin
/plugin install project-bootstrap
```

### Option 2: Add marketplace to settings

Add to your `~/.claude/settings.json`:

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

Then install:

```
/plugin install project-bootstrap
```

### Option 3: Local install (for development/testing)

```bash
git clone https://github.com/twisker/claude-project-bootstrap.git
cd your-project
claude --plugin-dir /path/to/claude-project-bootstrap
```

### Option 4: Copy as project skill

```bash
# Copy into your project's .claude/skills/ directory
mkdir -p .claude/skills/project-bootstrap
cp -r skills/project-bootstrap/* .claude/skills/project-bootstrap/
```

## Usage

After installation, in any Claude Code session:

```
/project-bootstrap
```

Or with a PRD document:

```
/project-bootstrap ./docs/PRD.md
```

Or with a description:

```
/project-bootstrap "A SaaS dashboard for e-commerce customer service analytics"
```

Claude will then guide you through an interactive dialogue to gather requirements before generating all files.

## Plugin Structure

```
claude-project-bootstrap/
├── .claude-plugin/
│   ├── plugin.json              # Plugin manifest
│   └── marketplace.json         # Marketplace catalog
├── skills/
│   └── project-bootstrap/
│       ├── SKILL.md             # Main skill definition
│       └── references/          # Template files for generation
│           ├── collaboration-template.md
│           ├── tech-spec-template.md
│           ├── arch-spec-template.md
│           ├── sprint-plan-template.md
│           ├── current-sprint-template.md
│           ├── module-spec-template.md
│           ├── test-registry-template.md
│           ├── validation-template.md
│           ├── human-todo-template.md
│           ├── claude-md-template.md
│           └── scripts-template.md
├── README.md
└── LICENSE
```

## Language

The default output language is **Chinese** (matching the original project context). Claude will follow the user's language preference during the dialogue phase -- if you speak English, it will generate English files.

## License

MIT
