---
id: task_31
title: "Remove manual company selection UI from dashboard"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T20:16:24.173960Z
completed_at: 2026-03-21T20:45:31.062622Z
updated_at: 2026-03-21T20:45:31.062619Z
---

## Description

Remove the company selector dropdown/picker from the TopHeader and other UI components. Company should no longer be user-selectable. Clean up any company selection logic that's no longer needed.

ACs:
- Company selector removed from TopHeader
- No manual company selection UI visible
- Cleaner, focused dashboard UI
- Navigation no longer depends on selected company

## Result

Build passes. Here's the summary:

### Changes Made

**`src/components/layouts/TopHeader.vue`**
- Removed `useActiveCompany` import and usage
- Removed the company name badge from the header

**`src/pages/AgentsPage.vue`**
- Removed company name badge from the page header

**`src/pages/OrgChartPage.vue`**
- Removed company name badge from the page header

**`src/pages/MetricsPage.vue`**
- Removed "Company" row from the system status panel

### ACs Verified
- ✅ **Company selector removed from TopHeader** — badge and composable import removed
- ✅ **No manual company selection UI visible** — all company name badges removed from page headers
- ✅ **Cleaner, focused dashboard UI** — headers now show only relevant actions
- ✅ **Navigation no longer depends on selected company** — company is auto-resolved via `useActiveCompany` composable (no selection logic exists)

Zero new TypeScript errors. Production build successful.
