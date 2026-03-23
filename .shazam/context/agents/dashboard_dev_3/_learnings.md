### [2026-03-21 15:26] From: Build Agent and Company Management with Org Chart visualization
- recursive `OrgTreeNode` component
- Project uses Vue.js

### [2026-03-21 20:01] From: Add debugging and logging for WebSocket data flow
- with error details on failure

### [2026-03-21 20:09] From: Fix company selection and global state initialization
- company name saved to `shazam:activeCompany` key, restored on page load
- company name from `localStorage`
- and persisted
- composable

### [2026-03-21 20:19] From: Update all dashboard pages to use automatically-loaded company
- `activeCompany.value.name`

### [2026-03-21 20:25] From: Ensure WebSocket events are processed and displayed
- wildcard listener with event type filtering

### [2026-03-21 20:32] From: Investigate and fix Memory page loading issue
- hide the tree

### [2026-03-21 20:34] From: Implement real-time agent status updates via WebSocket
- with distinct colors

### [2026-03-21 20:42] From: Document Shazam.dev color palette and design specifications
- (amber/yellow), error (red), info (blue), purple, cyan — with light/default/dark variants matching StatusBadge impleme

### [2026-03-21 20:43] From: Load current company automatically on dashboard startup
- hammer the backend.

### [2026-03-21 20:45] From: Remove manual company selection UI from dashboard
- company** — company is auto-resolved via `useActiveCompany` composable (no selection logic exists)
- and usage - Removed the company
- selected company

### [2026-03-21 20:49] From: Fix tasks list and cost calculation display on Dashboard
- on initial load. So cost displayed `<$0.01` until the first poll completed.

### [2026-03-21 20:52] From: Replace all dashboard buttons with standardized Button component
- AppButton from
- bg-yellow-600/20 text-yellow-300 hover:bg-yellow-600/30 focus:ring-yellow-500\n- info: bg-purple-600/20 text-purple-300

### [2026-03-21 21:01] From: Refactor MemoryBrowserPage with tree traversal and file loading logic extracted
- only props

### [2026-03-21 21:04] From: Refactor MetricsPage with chart rendering and data transformation logic extracted
- only props and pure imported functions

### [2026-03-21 21:11] From: Audit dashboard buttons and define design standard
- AppButton correctly

### [2026-03-21 21:30] From: SessionsPage Visual Enhancement
- the design system `stat-card`/`stat-value`/`stat-label` classes with responsive `grid-cols-2 sm:grid-cols-4`.

### [2026-03-21 21:37] From: MemoryBrowserPage & ConfigPage Visual Redesign
- file state**: `bg-shazam-500/10` with `ring-1 ring-inset ring-shazam-500/20`

