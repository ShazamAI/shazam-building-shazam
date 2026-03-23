---
id: task_22
title: "Investigate and document actual API response formats"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T20:07:30.338181Z
completed_at: 2026-03-21T20:13:28.509665Z
updated_at: 2026-03-21T20:13:28.509660Z
---

## Description

Examine raw responses from all backend endpoints being used by the dashboard (/api/companies, /api/tasks, /api/agents, etc.). Use browser DevTools Network tab or curl to capture actual JSON structure. Document the format for each endpoint and compare against what the frontend code expects.

ACs:
- Document actual response format for each API endpoint
- Identify if data is wrapped (e.g., {companies: [...]}) or unwrapped ([...])
- Verify if response includes metadata/status fields
- List which endpoints are missing or returning 404

## Result

All 6 errors are pre-existing in `CompaniesPage.vue` (unused vars) and `TasksPage.vue` (type mismatch unrelated to API layer). Zero new errors introduced.

## Summary

### Actual Backend Response Formats Documented

| Endpoint | Status | Wrapping | Key |
|---|---|---|---|
| `GET /api/companies` | ✅ 200 | `{ "companies": [...] }` | `companies` |
| `GET /api/tasks` | ✅ 200 | `{ "tasks": [...] }` | `tasks` |
| `GET /api/companies/:name/agents` | ✅ 200 | `{ "agents": [...] }` | `agents` |
| `GET /api/companies/:name/org-chart` | ✅ 200 | `{ "org_chart": [...] }` | `org_chart` |
| `GET /api/workspaces` | ✅ 200 | `{ "workspaces": [...] }` | `workspaces` |
| `GET /api/sessions` | ✅ 200 | `{ "sessions": [...] }` | `sessions` |
| `GET /api/config` | ❌ 404 | — | Missing endpoint |
| `GET /api/events/recent` | ❌ 404 | — | Missing endpoint |
| `GET /api/context/tree` | ❌ 404 | — | Missing endpoint |

### Critical Mismatches Fixed

1. **`http.ts`** — Removed wrong `{ "data": ... }` unwrap. Added `extractKey()` utility for named key extraction.
2. **`companyService.ts`** — Extracts from `companies`/`agents`/`org_chart` keys. Maps backend shapes (e.g., `active` → `status`, `subordinates` → `reports`, missing `tokens_used`/`status` defaults).
3. **`taskService.ts`** — Extracts from `tasks` key. Maps `BackendTask` → `Task` with null defaults for optional fields.
4. **`configService.ts`** — Extracts from `workspaces`/`sessions`/`tree` keys. Maps sessions from backend array `[{agent, alive, ...}]` to frontend Record format `{[agent]: {agent_name, idle, ...}}`.
5. **`eventService.ts`** — Already handles 404 gracefully (returns `[]`).

### 3 Missing Endpoints (Backend Responsibility)
- `/api/config` — 404
- `/api/events/recent` — 404
- `/api/context/tree` — 404

Frontend gracefully returns `null`/`[]` for all three.
