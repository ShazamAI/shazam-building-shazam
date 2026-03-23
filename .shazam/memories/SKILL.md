---
name: skill-index
description: Root index for project skill memory - Shazam AI Company Framework
tags: index, navigation, shazam
---

## 🎯 Project Skills Index

Shazam is an AI-driven multi-agent framework for autonomous software development. This index provides navigation to all skill memories.

---

## 📋 Project Foundation

Essential reading for understanding the overall project:

- **[project/overview.md](project/overview.md)** — Project mission, goals, workspace structure, team organization
- **[project/architecture.md](project/architecture.md)** — Architecture patterns, module organization, design principles
- **[project/conventions.md](project/conventions.md)** — Code style, naming conventions, Vue 3 patterns, TypeScript standards

---

## 📏 Domain Rules

Standards that govern how we work:

- **[rules/testing.md](rules/testing.md)** — Testing strategy, QA workflows, test patterns, coverage standards
- **[rules/git-workflow.md](rules/git-workflow.md)** — Git branches, commit conventions, PR workflow, conflict resolution

---

## 👥 Agent Memories

Role-specific context for each team member:

### Engineering Manager
- **[agents/manager.md](agents/manager.md)** — Role context, task decomposition, team delegation, dispatcher responsibilities

### Dashboard Team (Vue 3 + Tailwind)
- **[agents/pm_dashboard.md](agents/pm_dashboard.md)** — Dashboard PM role, team coordination, architecture knowledge
- **[agents/dashboard_dev_1.md](agents/dashboard_dev_1.md)** — Senior Frontend Developer (Dashboard)
- **[agents/dashboard_dev_2.md](agents/dashboard_dev_2.md)** — Senior Frontend Developer (Dashboard)
- **[agents/dashboard_dev_3.md](agents/dashboard_dev_3.md)** — Senior Frontend Developer (Dashboard)

### VS Code Extension Team (TypeScript + VS Code API)
- **[agents/pm_vscode.md](agents/pm_vscode.md)** — VS Code PM role, team coordination, extension architecture
- **[agents/vscode_dev_1.md](agents/vscode_dev_1.md)** — Senior VS Code Extension Developer
- **[agents/vscode_dev_2.md](agents/vscode_dev_2.md)** — Senior VS Code Extension Developer
- **[agents/vscode_dev_3.md](agents/vscode_dev_3.md)** — Senior VS Code Extension Developer

---

## 🏗️ Architectural Decisions

Important decisions are stored in `decisions/` directory (add decision files as needed):
- Each decision should document problem, options, choice, and rationale
- Use: `decisions/ADR-000-decision-title.md`

---

## 🗂️ File Structure

```
.shazam/memories/
├── SKILL.md                    # This index
├── project/
│   ├── overview.md            # Project mission and goals
│   ├── architecture.md        # Module organization
│   └── conventions.md         # Code standards
├── rules/
│   ├── testing.md            # Testing and QA workflows
│   └── git-workflow.md       # Git conventions
├── agents/
│   ├── manager.md
│   ├── pm_dashboard.md
│   ├── pm_vscode.md
│   ├── dashboard_dev_1-3.md
│   └── vscode_dev_1-3.md
└── decisions/
    └── (Add ADR files as needed)
```

---

## 🎓 How to Use This Knowledge Base

1. **New to the project?** Start with [project/overview.md](project/overview.md)
2. **Need code standards?** Read [project/conventions.md](project/conventions.md)
3. **Setting up your role?** Find your agent memory file
4. **Git question?** Check [rules/git-workflow.md](rules/git-workflow.md)
5. **Testing question?** Check [rules/testing.md](rules/testing.md)

---

## 📝 Notes

- All files use markdown with YAML frontmatter
- Cross-references use relative paths: `[name](./relative/path.md)`
- Agent memories are personal and role-specific
- Project skills are shared team knowledge
- Rules are absolute standards for this project
