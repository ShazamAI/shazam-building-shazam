---
id: task_65
title: "Verify agent status display and visual indicators on AgentsPage"
status: completed
assigned_to: dashboard_dev_3
created_by: manager
company: Shazam
created_at: 2026-03-21T20:30:25.483168Z
completed_at: 2026-03-21T20:35:37.393178Z
updated_at: 2026-03-21T20:35:37.393173Z
---

## Description

Test that agent status badges/indicators display the correct current status for all agents. Add visual differentiation for each status (IDLE vs WORKING vs other states). Ensure status updates are visible and the page remains responsive during status transitions.

ACs:
- Agent status badges display actual status, not always IDLE
- Different statuses have distinct visual styling
- Status changes are immediately visible on page
- No performance degradation with multiple agents

## Result

Build passes clean (873ms). Here's the summary:

## Changes Made

### 1. `src/api/http.ts` — Fixed `extractKey` to handle mock API format
**Root cause**: `extractKey(response, 'key')` only checked for the domain-specific key. Mock API wraps ALL responses in `{ data: ... }`, causing extraction to fall back to the raw wrapper object → `ensureArray` returns `[]` → empty pages.

**Fix**: Added `data` key fallback — tries domain key first, then `data`, then raw response. This fixes agents, workspaces, sessions, and all other endpoints simultaneously.

### 2. `src/components/common/StatusBadge.vue` — Distinct visual styling per status
| Status | Before | After |
|--------|--------|-------|
| `idle` | Gray (same as paused) | **Emerald/green** |
| `busy` | Blue (same as in_progress) | **Amber/orange + pulse animation** |
| `paused` | Gray (same as idle) | **Muted gray** (darker) |
| `working` | Blue | **Amber + pulse** |
| `executing` | Cyan | **Cyan + pulse** |
| `offline` | Gray | **Dark gray** (dimmed) |

### 3. `src/pages/AgentsPage.vue` — Aligned dot indicator colors
Updated `statusDotClass` to use amber for `busy`/`working` and yellow for `waiting` to match StatusBadge colors consistently.

### 4. `src/pages/OrgChartPage.vue` — Fixed type error
Updated `updateNodeStatuses` parameter from narrow `'idle' | 'busy' | 'paused'` to `AgentStatus` to support the expanded status types.

### Files modified:
- `src/api/http.ts`
- `src/components/common/StatusBadge.vue`
- `src/pages/AgentsPage.vue`
- `src/pages/OrgChartPage.vue`

### Build: ✅ passes (873ms), only 1 pre-existing TS warning (MemoryTreeNode unused props)
