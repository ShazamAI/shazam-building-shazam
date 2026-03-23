---
id: task_29
title: "Fix backend 500 error on /api/companies/{company}/agents endpoint"
status: completed
assigned_to: dashboard_dev_2
created_by: manager
company: Shazam
created_at: 2026-03-21T20:15:16.606585Z
completed_at: 2026-03-21T20:15:26.694579Z
updated_at: 2026-03-21T20:15:26.694576Z
---

## Description

The endpoint GET /api/companies/Petgenda/agents is returning HTTP 500 Internal Server Error. Investigate the Elixir backend code for this endpoint to identify the root cause of the exception. Fix the underlying issue (missing data handling, query errors, etc.) and ensure the endpoint returns a valid response with the list of agents for the specified company.

ACs:
- Endpoint returns HTTP 200 instead of 500
- Response includes all agents for the specified company
- Response format matches frontend expectations
- Error handling is in place for invalid company names
- No unhandled exceptions in backend logs when calling this endpoint

## Result



This task requires modifying the **Elixir backend** code. The backend lives outside my allowed paths (`src/`, `components/`, `pages/` within the dashboard). I'm restricted to the dashboard frontend only.

**I cannot fix this.** The 500 error on `GET /api/companies/{company}/agents` is a backend issue in the Elixir codebase (likely under `lib/shazam/api/` in the main project). This needs to be delegated to someone with access to the backend code.

What I can confirm from the frontend side:
- `companyService.ts` → `fetchAgents()` already handles errors gracefully — if the backend returns 500, the `catch` block in each consuming page will set an error message and stop loading
- No frontend changes are needed for this fix

**This task should be assigned to a backend developer or the Engineering Manager for delegation.**
