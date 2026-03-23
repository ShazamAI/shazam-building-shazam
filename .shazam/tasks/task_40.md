---
id: task_40
title: "Load current company automatically on dashboard startup"
status: completed
assigned_to: dashboard_dev_2
created_by: manager
company: Shazam
created_at: 2026-03-21T20:21:14.403748Z
completed_at: 2026-03-21T20:43:49.448049Z
updated_at: 2026-03-21T20:43:49.448045Z
---

## Description

Implement automatic company loading from the backend when the dashboard initializes. Query the backend for the currently active company (the one Shazam is running) and set it as the active company in the global state. This should happen automatically without user interaction or selection.

ACs:
- On app load, fetch current company from backend
- Active company is set in global state automatically
- No user action needed to set company
- Dashboard always reflects the company Shazam is currently running
- Company updates if Shazam switches to different company

## Result

All ACs verified:

- **On app load, fetch current company from backend** — `loadCompanies()` called immediately in `onMounted`
- **Active company set in global state automatically** — `useActiveCompany` computed resolves `status === 'active'` company first, then first available. No user action needed.
- **No user action needed** — zero `selectCompany` calls anywhere, no selection UI
- **Dashboard always reflects current Shazam company** — `activeCompany` computed reactively updates when `companies` ref changes from any `loadCompanies()` call
- **Company updates if Shazam switches** — Two mechanisms:
  1. **WebSocket**: listens for `company_started`, `company_stopped`, `company_switched`, `company_updated`, `company_status_change` events → triggers immediate `loadCompanies()`
  2. **Polling**: `setInterval` every 30s calls `loadCompanies()` as fallback if WS events don't fire
  
The `loadCompanies()` function has built-in deduplication (`if (loadPromise) return loadPromise`) so concurrent calls from polling + WS + page-level refreshes don't hammer the backend.
