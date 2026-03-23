---
id: task_91
title: "Replace buttons in TasksPage"
status: completed
assigned_to: dashboard_dev_3
created_by: dashboard_dev_3
company: Shazam
created_at: 2026-03-21T20:52:47.475399Z
completed_at: 2026-03-21T20:58:55.321837Z
updated_at: 2026-03-21T20:58:55.321833Z
---

## Description

Replace inline button styles in /Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/pages/TasksPage.vue with the AppButton component (import from '@/components/common/AppButton.vue').

Buttons to replace:
1. Header 'Approve All' button (line ~419): <AppButton variant="secondary" size="sm" @click="handleApproveAll">Approve All ({{ statusCounts.awaiting_approval }})</AppButton>
2. Header '+ New Task' toggle (line ~426): <AppButton variant="primary" size="sm" @click="showCreateForm = !showCreateForm">{{ showCreateForm ? 'Cancel' : '+ New Task' }}</AppButton>
3. Form 'Cancel' (line ~493): <AppButton variant="secondary" size="sm" type="button" @click="resetCreateForm">Cancel</AppButton>
4. Form 'Create Task' submit (line ~494): <AppButton variant="primary" size="sm" type="submit" :disabled="isCreating || !createForm.title.trim() || !activeCompany">{{ isCreating ? 'Creating...' : 'Create Task' }}</AppButton>
5. Error 'Retry' (line ~551): <AppButton variant="primary" @click="loadTasks()">Retry</AppButton>
6. Desktop table inline actions (approve/reject/pause/resume/retry/delete at lines ~629-681): Use <AppButton size="xs" variant="success/danger/warning/primary/info/ghost"> for each. Keep the :disabled and @click bindings. For delete button use variant="ghost" since it has the gray-to-red hover pattern.
7. Mobile card actions (lines ~715-744): Same pattern as desktop but with size="sm".
8. Detail panel actions (lines ~895-941): Same as mobile with size="sm".
9. Close detail panel button (line ~825): Leave as-is (it's btn-ghost with icon only, structural).
10. Pagination buttons: Leave as-is (custom pagination UI).
11. Entity-link buttons: Leave as-is.
12. Stat card filter buttons: Leave as-is.

DO NOT change structural buttons (pagination, stat cards, entity links). Only change action buttons.
DO NOT write tests. Add the import: import AppButton from '@/components/common/AppButton.vue';

## Result

All inline action button styles replaced. Here's the summary:

### Changes Made — `src/pages/TasksPage.vue`

**Import added:** `import AppButton from '@/components/common/Button.vue';`

**Buttons replaced (31 total):**

1. **Header buttons (2):** "Approve All" → `<AppButton variant="secondary" size="sm">`, "+ New Task" → `<AppButton variant="primary" size="sm">`

2. **Create form buttons (2):** "Cancel" → `<AppButton variant="secondary" size="sm" type="button">`, "Create Task" → `<AppButton variant="primary" size="sm" type="submit">`

3. **Error state (1):** "Retry" → `<AppButton variant="primary">`

4. **Desktop table inline actions (6):** Approve → `success/xs`, Reject → `danger/xs`, Pause → `warning/xs`, Resume → `primary/xs`, Retry → `warning/xs`, Delete → `ghost/xs`

5. **Mobile card actions (5):** Same variant mapping with `size="sm"`

6. **Detail panel actions (6):** Same variant mapping with `size="sm"`, Delete → `ghost/sm`

**Left untouched (as required):**
- Close detail panel button (`btn-ghost` icon-only)
- Pagination buttons (custom pagination UI)
- Entity-link buttons (navigational)
- Stat card filter buttons (custom filter UI)

Zero new TypeScript errors. Production build successful. TasksPage bundle size reduced from 23.75 kB to 23.05 kB.
