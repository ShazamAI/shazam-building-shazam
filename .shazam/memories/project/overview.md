---
name: project-overview
description: Shazam project overview, mission, goals, and tech stack
tags: project, overview, shazam, architecture
---

## Project Mission
**Coordinate multiple AI agents in hierarchical teams. Define roles, delegate tasks, and let your AI company ship software autonomously.**

Shazam is an AI-driven software development framework that enables autonomous multi-agent teams to collaborate on building complex applications. The platform uses Claude AI agents with defined roles, hierarchical supervision, and specialized capabilities to coordinate development work across multiple modules.

## Project Structure

Shazam is organized as a **multi-workspace mono-repository** with two independent domain applications:

### Workspaces

1. **Dashboard** (`/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard`)
   - Vue 3 web dashboard for monitoring and controlling Shazam
   - TypeScript + Tailwind CSS + Vite build system
   - Key modules: src/, components/, pages/, composables/, assets/

2. **VS Code Extension** (`/Users/raphaelbarbosa/Projects/ShazamAI/shazam-vscode`)
   - VS Code extension for Shazam integration
   - TypeScript + VS Code Extension API
   - Key modules: src/, extension/, webview/

## Tech Stack

### Dashboard
- **Language**: TypeScript
- **Framework**: Vue 3 (Composition API)
- **Styling**: Tailwind CSS
- **Build Tool**: Vite
- **Runtime**: Node.js

### VS Code Extension
- **Language**: TypeScript
- **Framework**: VS Code Extension API
- **Build Tool**: esbuild/webpack (typical for VS Code extensions)
- **Runtime**: Node.js (V8 engine via VS Code)

## Team Structure

The project uses a hierarchical agent organization:

```
Engineering Manager (manager)
├── PM Dashboard (pm_dashboard)
│   ├── Senior Frontend Developer (dashboard_dev_1)
│   ├── Senior Frontend Developer (dashboard_dev_2)
│   └── Senior Frontend Developer (dashboard_dev_3)
└── PM VS Code (pm_vscode)
    ├── Senior VS Code Developer (vscode_dev_1)
    ├── Senior VS Code Developer (vscode_dev_2)
    └── Senior VS Code Developer (vscode_dev_3)
```

## Key Goals

1. **Autonomous Agent Coordination** - Enable AI agents to work independently with minimal human oversight
2. **Modular Development** - Maintain clear separation between dashboard and extension domains
3. **Scalable Architecture** - Support adding new features and agents without major restructuring
4. **Type Safety** - Strict TypeScript usage across all modules
5. **Developer Experience** - Smooth tooling, clear conventions, and comprehensive documentation

## Configuration

See [../rules/git-workflow.md](../rules/git-workflow.md) for branch and commit conventions.
See [./architecture.md](./architecture.md) for module organization and patterns.
See [./conventions.md](./conventions.md) for code style and naming standards.
