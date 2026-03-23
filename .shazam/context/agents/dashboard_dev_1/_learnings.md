### [2026-03-21 14:24] From: Create testing and git workflow domain rules
- test, QA does), testing architecture for both dashboard and VS Code extension, coverage targets (80%/70%/90%), QA workfl

### [2026-03-21 15:19] From: Scaffold Vue 3 project and build core infrastructure
- Tailwind CSS utility classes exclusively
- Project uses Vue.js
- Project uses Node.js/npm

### [2026-03-21 15:22] From: Build real-time Event Feed and Metrics Dashboard
- when user scrolls up
- auto-scroll

### [2026-03-21 19:58] From: Fix DashboardPage.vue error handling for undefined tasks
- banner instead of a broken page.
- banner shown when backend APIs fail, informing users data may be unavailable.

### [2026-03-21 20:03] From: Fix loading state management in WebSocket integration
- break other handlers or crash the composable

### [2026-03-21 20:35] From: Verify agent status display and visual indicators on AgentsPage
- extraction to fall back to the raw wrapper object → `ensureArray` returns `[]` → empty pages.
- (MemoryTreeNode unused props)

### [2026-03-21 20:44] Fix missing bg-surface Tailwind class in dashboard CSS
Everything is already working correctly:
1. **`surface` colors are defined** in `tailwind.config.js` under `theme.extend.colors.surface` with `DEFAULT: '#0a0a0f'`, `card: '#12121a'`, and `card-hover: '#1a1a24'`
2. **`bg-surface`** resolves to `background-color: #0a0a0f` (used on `body`)
3. **`bg-surf

### [2026-03-21 20:44] From: Implement /api/events/recent endpoint in backend
- changes to the Elixir backend which

### [2026-03-21 20:49] From: Investigate and fix Config page loading issue
- banner when config endpoint is unavailable

### [2026-03-21 20:51] From: Fix missing P, R, D display and page refresh issue on Dashboard
- excessive re-renders

### [2026-03-21 21:05] From: Standardize error handling and loading states across all refactored services
- centralized

### [2026-03-21 21:19] From: Agent Page: Workspace/Domain Separation & Redesign
- the new AgentCard component with staggered animation delays

### [2026-03-21 21:37] From: TasksPage Visual Polish & Component Enhancement
- row: `bg-shazam-500/[0.06]` with golden accent border
- state with shazam shadow glow
- trash icon instead of

