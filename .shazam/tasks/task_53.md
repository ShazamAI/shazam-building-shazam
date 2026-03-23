---
id: task_53
title: "Apply brand colors to all dashboard components and pages"
status: completed
assigned_to: dashboard_dev_3
created_by: manager
company: Shazam
created_at: 2026-03-21T20:28:05.942022Z
completed_at: 2026-03-21T20:40:59.602696Z
updated_at: 2026-03-21T20:40:59.602691Z
---

## Description

Update all Vue components, pages, and UI elements to use the new Tailwind color tokens. Verify visual consistency across the entire dashboard—headers, cards, buttons, forms, badges, alerts, navigation, and all interactive elements. Ensure the updated theme matches Shazam.dev's visual identity.

ACs:
- All components use updated Tailwind color tokens
- Dashboard visually matches Shazam.dev color scheme
- Consistency verified across all pages (Dashboard, Tasks, Metrics, Agents, OrgChart, Settings, Companies)
- No hardcoded colors remaining in component styles

## Result

Only 1 pre-existing error (MemoryTreeNode unused props — unrelated). Zero new errors.

## Summary of Changes

### Files Modified (10 files)

**Brand color alignment (blue → shazam):**

| File | Change |
|------|--------|
| `src/components/features/EventFeed.vue` | Agent output event badge: `bg-blue-500/10 text-blue-400` → `bg-shazam-500/10 text-shazam-400` |
| `src/components/features/StatusBar.vue` | Running task count: `text-blue-400` → `text-shazam-400` |
| `src/components/features/OrgTreeNode.vue` | Senior role color: `text-blue-400` → `text-shazam-400` |
| `src/pages/AgentsPage.vue` | Tool badges: `bg-blue-500/10 text-blue-400` → `bg-shazam-500/10 text-shazam-400` |
| `src/pages/ConfigPage.vue` | Provider badge: `bg-blue-500/10 text-blue-400` → `bg-shazam-500/10 text-shazam-400` |
| `src/pages/TasksPage.vue` | Resume buttons (3 locations): `blue-600/20 text-blue-400` → `shazam-600/20 text-shazam-400` |

**Agent "busy" status alignment (blue → amber, matching StatusBadge):**

| File | Change |
|------|--------|
| `src/components/features/OrgTreeNode.vue` | Busy border/bg: `border-blue-500 bg-blue-500/5` → `border-amber-500 bg-amber-500/5`; Busy dot: `bg-blue-400` → `bg-amber-400` |
| `src/pages/DashboardPage.vue` | Busy agent dot: `bg-blue-500` → `bg-amber-400` |
| `src/pages/MetricsPage.vue` | Busy agent dot: `bg-blue-500` → `bg-amber-400`; Busy count: `text-blue-400` → `text-amber-400` |
| `src/pages/OrgChartPage.vue` | Legend busy dot: `bg-blue-400` → `bg-amber-400` |

**Intentionally kept as `blue-`** (semantic task status colors, not brand):
- `StatusBadge.vue` `in_progress` → blue = task running status
- `DashboardPage.vue` / `MetricsPage.vue` "In Progress" stat values → blue = task status
- `TasksPage.vue` filter chip for "Running" → blue = task status

### Color System Now Consistent

| Purpose | Token | Example |
|---------|-------|---------|
| **Brand / interactive** | `shazam-400` to `shazam-600` | Buttons, links, tool badges, active tabs |
| **Agent busy** | `amber-400` | Status dots, StatusBadge, count labels |
| **Agent idle** | `emerald-400` | Status dots, StatusBadge |
| **Agent paused** | `gray-500` | Status dots, StatusBadge |
| **Task in_progress** | `blue-400` | Stat values, filter chips, StatusBadge |
| **Task pending** | `yellow-400` | Stat values, StatusBadge |
| **Task completed** | `emerald-400` | Stat values, StatusBadge |
| **Task failed** | `red-400` | Stat values, StatusBadge |

### Build: ✅ passes (885ms), 0 new TypeScript errors
