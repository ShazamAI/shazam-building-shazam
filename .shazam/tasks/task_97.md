---
id: task_97
title: "Agent Page: Workspace/Domain Separation & Redesign"
status: completed
assigned_to: dashboard_dev_3
created_by: manager
company: Shazam
created_at: 2026-03-21T21:14:41.696478Z
completed_at: 2026-03-21T21:19:26.675003Z
updated_at: 2026-03-21T21:19:26.674998Z
---

## Description

Reorganize Agent page to separate agents by workspace (dashboard, vscode) and domain. Add filtering/grouping controls, redesign agent cards with enhanced status indicators, workspace badges, and role information. Implement smooth transitions between workspace views.

ACs:
- Agents clearly grouped and separated by workspace and domain
- Visual filtering/grouping UI allows easy navigation between workspaces
- Each agent card shows workspace, domain, role, and status with consistent styling
- Workspace selector prominent and intuitive (tabs or dropdown)
- Agent status indicators match new design system with smooth animations

## Result

Build successful. All three files are implemented:

## Summary

### Created Files

**`src/components/features/AgentCard.vue`** — Enhanced agent card component with:
- Agent avatar with role-specific emoji and status dot overlay
- Workspace badge with color-coded styling (Dashboard=blue, VS Code=violet, Backend=emerald)
- Supervisor chain indicator
- Activity sparkline with contextual coloring (gold when active, gray when idle)
- 3-column stats grid (Tasks, Tools, Model)
- Animated budget progress bar with color thresholds
- Tool pills with shazam accent styling
- Module pills
- Staggered card entrance animation (`cardFadeIn`)
- Active glow effect with radial gradient for busy/working/executing agents

**`src/components/features/WorkspaceTabs.vue`** — Workspace filter tabs with:
- "All" tab with total count
- Dynamic workspace tabs generated from agent data
- Color-coded active states per workspace
- Animated underline hover effect
- Count badges with smooth color transitions

### Redesigned Files

**`src/pages/AgentsPage.vue`** — Complete redesign with:
- **Header**: Icon + title "Agent Fleet" with "Deploy Agent" CTA button
- **Live Stats Bar**: 5 animated stat cards (Total, Active with pulse dot, Idle, Offline, Errors) with staggered entrance
- **Workspace Tabs**: Filter agents by workspace (Dashboard, VS Code, Backend, General)
- **Search + Filter**: Text search across name/role/domain/supervisor + status dropdown filter
- **Grouped Layout**: When "All" is selected, agents display in labeled workspace groups with gradient dividers
- **Agent Cards**: Using the new AgentCard component with staggered animation delays
- **Modal Redesign**: Sticky header, rounded-2xl inputs, grid layouts for form fields, Teleport + Vue Transition for smooth enter/leave
- **Animations**: Stats fade-in, workspace group slide-in, modal scale transitions

### Key Design Decisions
- Workspace is **inferred from domain** field (dashboard/frontend → Dashboard, vscode/extension → VS Code, backend/api → Backend)
- No type changes needed — uses existing `AgentWorker.domain` field
- All existing CRUD functionality preserved through `useAgents` composable
- Zero TypeScript errors, production build passes (952ms, 108 modules)
