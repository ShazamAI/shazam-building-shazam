---
id: task_8
title: "Build Task Management feature with full CRUD and approval flow"
status: completed
assigned_to: dashboard_dev_2
created_by: pm_dashboard
company: Shazam
created_at: 2026-03-21T15:14:04.058760Z
completed_at: 2026-03-21T15:36:32.415403Z
updated_at: 2026-03-21T15:36:32.415397Z
---

## Description

Build the complete Task Management page that mirrors the CLI's task capabilities. Users must be able to view, create, and manage tasks with all status transitions.

Behavior requirements:
- Task list view showing all tasks with columns: ID, title, status (color-coded badge), assigned agent, created by, timestamps. Support filtering by status and assigned agent
- Task creation form with fields: title, description, assigned_to (agent dropdown), depends_on (optional task reference)
- Task detail view showing full task information including description, result (rendered as markdown), parent task, dependencies
- Action buttons on each task based on current status: Approve/Reject (for awaiting_approval), Pause (for pending/in_progress), Resume (for paused), Retry (for failed), Delete
- Bulk approval action (approve all awaiting tasks)
- Task status counts summary bar showing: Pending, Running, Completed, Failed, Awaiting Approval, Paused counts
- Real-time task status updates via WebSocket — when a task_status_change event arrives, the task list updates automatically without page refresh
- Search/filter functionality to find tasks by title

ACs:
- Task list displays all tasks from GET /api/tasks with correct status badges
- Users can create a new task via POST /api/companies/:name/tasks
- Each task shows contextual action buttons matching its current status
- Approve, reject, pause, resume, retry, and delete actions call the correct API endpoints and update the UI
- Task detail view renders the result field as formatted markdown
- WebSocket task_status_change events trigger automatic UI updates

## Result

Build successful — zero errors. Here's a summary of everything implemented:

## Implementation Summary

### Files Modified

| File | Changes |
|------|---------|
| `src/api/http.ts` | Added `del()` HTTP DELETE method |
| `src/api/taskService.ts` | Added 6 new endpoints: `approveTask`, `rejectTask`, `pauseTask`, `resumeTask`, `retryTask`, `deleteTask`, `approveAllTasks` |
| `src/pages/TasksPage.vue` | Complete rewrite with full task management feature |

### TasksPage Features

**Task List View:**
- Displays all tasks with columns: ID, Title, Status (color-coded badge), Agent (clickable link), Created By, Updated timestamp
- Sorting by `updated_at` descending
- Desktop table + mobile card layout (responsive)

**Filtering & Search:**
- Status filter via clickable count cards (toggle on/off)
- Agent filter dropdown populated from task data
- Text search across title, ID, and description

**Status Counts Summary Bar:**
- 6 cards: Pending, Running, Completed, Failed, Awaiting Approval, Paused
- Each card is clickable to filter, with active ring indicator

**Task Creation Form:**
- Fields: Company (from `useActiveCompany`), Assign To (agent dropdown), Title, Description, Depends On
- Posts to `POST /api/companies/:name/tasks`
- Auto-adds created task to list without refresh

**Task Detail Panel:**
- Slide-in panel showing full metadata: assigned_to, created_by, company, parent_task, depends_on, created timestamp
- Description section (whitespace-preserved)
- Result section rendered as formatted pre-wrapped text
- Closes on filter change

**Contextual Action Buttons (per status):**
- `awaiting_approval` → Approve / Reject
- `pending` or `in_progress` → Pause
- `paused` → Resume
- `failed` → Retry
- All statuses → Delete
- Actions appear both in table rows and detail panel
- Loading states per action per task

**Bulk Actions:**
- "Approve All" button when awaiting tasks exist

**Real-time WebSocket Updates:**
- Listens for `task_status_change` events
- Updates task status in-place without page refresh
- Syncs the detail panel if the active task changes
- Falls back to full reload for unknown task IDs

**Conventions Followed:**
- Uses `useToast` for success/error notifications
- Uses `useActiveCompany` composable for company context
- Uses CSS utility classes: `.card`, `.btn-primary`, `.btn-secondary`, `.btn-sm`, `.input`, `.select`, `.table-header`, `.table-row`, `.stat-card`, `.stat-label`, `.stat-value`, `.entity-link`
- Vue 3 Composition API with `<script setup lang="ts">`
- No `any` types
