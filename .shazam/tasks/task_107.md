---
id: task_107
title: "Typography Hierarchy & Spacing Standardization"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T21:14:41.697077Z
completed_at: 2026-03-21T21:31:16.478113Z
updated_at: 2026-03-21T21:31:16.478110Z
---

## Description

Audit and standardize typography hierarchy across all pages (heading sizes, weights, line heights). Define and apply consistent spacing scale throughout dashboard. Ensure visual hierarchy is clear and professional appearance maintained.

ACs:
- Typography follows consistent hierarchy (H1, H2, H3, body, small)
- Spacing uses defined scale (8px, 16px, 24px, 32px, etc.)
- All text readable with proper contrast ratios
- Visual hierarchy clear on all pages
- Heading and body text proportions feel professional

## Result

Zero errors, build passes. Here's the summary of all changes:

## Summary: Typography Hierarchy & Spacing Standardization

### CSS Foundation (`src/styles/main.css`)
Added 6 new reusable typography utility classes to the component layer:

| Class | Purpose | Definition |
|-------|---------|------------|
| `.section-title` | Card/panel headings (H2/H3) | `text-sm font-semibold text-white sm:text-base` |
| `.section-subtitle` | Description text under section headers | `text-xs text-gray-500` |
| `.form-label` | Form field labels | `mb-1.5 block text-xs font-medium text-gray-400` |
| `.data-label` | Key-value pair labels | `text-xs font-medium text-gray-500` |
| `.data-value` | Key-value pair values | `text-sm text-gray-200` |
| `.micro-label` | Uppercase tracking labels | `text-[10px] font-semibold uppercase tracking-wider text-gray-600 sm:text-[11px]` |

Also fixed `.page-header` spacing (mb-4 → sm:mb-6 instead of md:mb-6) and `.page-title` breakpoint (sm:text-xl instead of md:text-xl) to match responsive patterns.

### Pages Standardized (6 files)
All pages now use `.page-title` and `.page-subtitle` classes:
- **TasksPage** — Replaced inline `text-xl font-bold` + `text-sm text-gray-500` with utility classes
- **ConfigPage** — Fixed `text-gray-400` → standardized to `text-gray-500` via `.page-subtitle`
- **SessionsPage** — Fixed `text-gray-400` → standardized subtitle color
- **MemoryBrowserPage** — Fixed `text-gray-400` → standardized subtitle color
- **OrgChartPage** — Changed `h2` → `h1`, replaced inline micro-labels with `.micro-label`
- **AgentsPage** — Replaced inline heading, stat labels with `.stat-label`, form labels with `.form-label`, modal titles with `.section-title`

### Feature Components Standardized (9 files)
All section headings now use `.section-title`, card structures use `.card-header` and `.card-body`:
- **EventFeed** — Header uses `card-header` + `section-title`
- **TaskOverview** — Uses `card-body` + `section-title`
- **AgentList** — Uses `card-body` + `section-title`
- **RecentTasks** — Uses `card-header` + `section-title`
- **ConfigGeneralTab** — Uses `card-header`, `section-title`, `data-label`, `data-value`
- **ConfigRalphTab** — Uses `card-header`, `section-title`, `section-subtitle`, `.input` class
- **ConfigAgentsTab** — Uses `card-header`, `section-title`, `section-subtitle`, `data-label`, `.badge`
- **ConfigPluginsTab** — Uses `card-header`, `section-title`, `section-subtitle`, `.badge`
- **ConfigTechStackTab** — Uses `card-header`, `section-title`, `section-subtitle`, `data-label`
- **ConfigWorkspacesTab** — Uses `card-header`, `card-body`, `section-title`, `.micro-label`

### Key Consistency Fixes
1. **Subtitle color**: `text-gray-400` → `text-gray-500` across all pages (was mixed)
2. **Heading elements**: All page titles now use `h1`, section titles use `h2`/`h3`
3. **Card padding**: All use responsive `.card-header` / `.card-body` instead of hardcoded `px-6 py-4`
4. **Stat labels**: All agent page stats use the global `.stat-label` class
5. **Form labels**: Consolidated to `.form-label` class in AgentsPage modal
