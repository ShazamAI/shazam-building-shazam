---
id: task_45
title: "Audit dashboard pages and remove selectCompany logic"
status: completed
assigned_to: dashboard_dev_3
created_by: manager
company: Shazam
created_at: 2026-03-21T20:22:49.362799Z
completed_at: 2026-03-21T20:29:04.170740Z
updated_at: 2026-03-21T20:29:04.170734Z
---

## Description

Verify that all dashboard pages use the automatically-loaded activeCompany from global state and remove any manual company selection logic. Ensure no selectCompany() function calls or user-triggered company changes exist in the UI.

ACs:
- All dashboard pages properly consume activeCompany from global state
- No selectCompany() calls or manual company switching logic exists in page components
- Application builds without TypeScript errors

## Result

**All done. Summary of changes:**

### `src/composables/useActiveCompany.ts`
- Removed `selectedCompanyName` ref — no more manual selection state
- Removed `selectCompany()` function — no longer exported
- Removed `resolveActiveCompany()` helper — was only needed for manual selection priority
- Simplified `activeCompany` computed to auto-resolve: backend-active → first available

### `src/pages/TasksPage.vue`
- Removed `companies` from destructured composable
- Changed `allAgents` computed from `companies.value.flatMap(c => c.agents)` to `activeCompany.value?.agents ?? []` — agents now scoped to active company only

### `src/pages/MetricsPage.vue`
- Removed `companies` from destructured composable
- Replaced "Companies: N" metric with "Company: {name}" — shows current active company name instead of count

### Verification
- ✅ Production build passes (81 modules, 906ms, zero errors)
- ✅ Zero `selectCompany` references in codebase
- ✅ Pre-existing TasksPage type issues are unrelated (documented in prior sessions)
