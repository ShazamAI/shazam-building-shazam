---
id: task_59
title: "Create reusable pagination component for dashboard"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T20:29:59.189025Z
completed_at: 2026-03-21T20:37:03.550398Z
updated_at: 2026-03-21T20:37:03.550392Z
---

## Description

Build a pagination component that can be reused across dashboard pages. The component should display current page number, total pages, and navigation controls (previous, next, jump to page). Support customizable page sizes and emit events when page changes. Ensure it's accessible and styled consistently with dashboard theme.

ACs:
- Pagination component accepts page number, total count, and page size as props
- Previous/Next buttons navigate between pages
- Component emits page-change event when user navigates
- Pagination matches dashboard styling and color scheme
- Component is reusable and documented for other pages

## Result

Build passes. Here's the summary:

## Created: `src/components/common/Pagination.vue`

A reusable pagination component following the dashboard's design system.

### Props
| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `page` | `number` | required | Current active page (1-based) |
| `totalItems` | `number` | required | Total items across all pages |
| `pageSize` | `number` | `10` | Items per page |
| `maxVisiblePages` | `number` | `5` | Max page buttons shown |
| `pageSizeOptions` | `number[]` | `[10, 25, 50]` | Page size selector options (empty array hides it) |

### Events
| Event | Payload | Description |
|-------|---------|-------------|
| `update:page` | `number` | Emitted when user navigates to a new page |
| `update:pageSize` | `number` | Emitted when user changes page size (auto-resets to page 1) |

### Usage
```vue
<Pagination
  :page="currentPage"
  :total-items="filteredItems.length"
  :page-size="pageSize"
  @update:page="currentPage = $event"
  @update:page-size="pageSize = $event"
/>
```

### Features
- **Smart ellipsis**: Shows `1 ... 4 5 6 ... 20` pattern for large page counts
- **Item range summary**: "Showing 1–10 of 247"
- **Page size selector**: Dropdown to change items per page
- **Accessibility**: `aria-label`, `aria-current="page"`, `aria-disabled` on all controls
- **Dashboard theme**: `gray-900` backgrounds, `shazam-600` active state, `gray-700` borders
- **Responsive**: Stacks vertically on mobile, horizontal on `sm+`
- **Edge-safe**: Clamps page numbers, hides when `totalItems === 0`

### Additional fix
- Fixed pre-existing build error in `src/api/taskService.ts` (operator precedence: `??` mixed with `||` without parentheses)

### Build: ✅ passes (916ms)
