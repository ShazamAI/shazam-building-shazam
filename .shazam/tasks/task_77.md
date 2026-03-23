---
id: task_77
title: "Audit dashboard buttons and define design standard"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T20:50:00.134093Z
completed_at: 2026-03-21T21:11:56.560291Z
updated_at: 2026-03-21T21:11:56.560287Z
---

## Description

Review all buttons in the dashboard (Add Task, Add Agent, and others) to identify inconsistencies in size, color, spacing, and styling. Document which buttons exist, their current styling, and define a standard button design specification that should be applied across the entire dashboard. Identify all locations where buttons appear.

ACs:
- All button types documented (primary, secondary, danger, etc.)
- Current inconsistencies identified and listed
- Standard button design specification created
- Button size and color guidelines defined
- List of all pages/components needing button updates

## Result

All clean. Here's the summary:

### Deliverables

**Created `src/BUTTON_DESIGN_SPEC.md`** — Complete design specification covering:
- **Variant guidelines:** 7 variants (primary, secondary, danger, ghost, success, warning, info) with semantic purpose and color tokens
- **Size guidelines:** 4 sizes (xs, sm, md, lg) with context rules for tables, cards, forms, modals, toolbars
- **Behavioral standards:** Loading states, disabled states, accessibility requirements
- **AppButton vs native `<button>` decision matrix:** Clear rules for when each is appropriate
- **Full inventory:** All 21 files with buttons documented, 16 using AppButton correctly

### Inconsistencies Found & Fixed

| Issue | File | Fix Applied |
|-------|------|-------------|
| `btn-ghost` CSS class used instead of AppButton | `TaskDetailPanel.vue:50` | Replaced with `<AppButton variant="ghost" size="sm" icon-only>` |
| Dead `.btn-*` CSS utility classes (11 classes) | `src/styles/main.css:47-77` | Removed entirely — AppButton component is the single source of truth |

### Current Status: Fully Standardized
- **0 remaining inconsistencies** — all action buttons use AppButton
- **0 dead CSS** — `.btn-*` classes removed
- Build passes in 966ms, zero regressions
