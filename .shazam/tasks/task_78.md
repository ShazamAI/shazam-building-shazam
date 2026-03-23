---
id: task_78
title: "Create reusable Button component following design standard"
status: completed
assigned_to: dashboard_dev_2
created_by: manager
company: Shazam
created_at: 2026-03-21T20:50:00.134173Z
completed_at: 2026-03-21T20:52:40.455432Z
updated_at: 2026-03-21T20:52:40.455425Z
---

## Description

Build a reusable Button component that enforces consistent sizing, colors, spacing, and styling across the dashboard. Support different button variants (primary, secondary, danger) and sizes (small, medium, large). Ensure the component integrates with the updated dashboard color scheme and Tailwind config.

ACs:
- Button component accepts variant and size props
- Component supports all required button states (default, hover, active, disabled)
- Styling matches dashboard design standard
- Component is documented and ready for use

## Result

Zero new errors, build passes.

## Created: `src/components/common/Button.vue`

A reusable Button component that integrates with the Shazam design system.

### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'primary' \| 'secondary' \| 'danger' \| 'ghost'` | `'primary'` | Visual style |
| `size` | `'sm' \| 'md' \| 'lg'` | `'md'` | Button size |
| `loading` | `boolean` | `false` | Shows spinner, disables interaction |
| `disabled` | `boolean` | `false` | Disables the button |
| `iconOnly` | `boolean` | `false` | Square padding for icon-only buttons |
| `as` | `'button' \| 'a'` | `'button'` | Render element (link support) |
| `type` | `'button' \| 'submit' \| 'reset'` | `'button'` | HTML button type |

### States covered
- **Default** — variant-specific background + text colors
- **Hover** — lighter/darker variant-specific color shift
- **Active** — deeper press state per variant
- **Disabled** — `opacity-50 + cursor-not-allowed`, interaction blocked
- **Loading** — animated spinner + disabled state + `aria-busy`
- **Focus** — `ring-2` with variant-appropriate ring color + surface offset

### Color mapping (from design tokens)
- **Primary**: `bg-shazam-500 text-gray-950` → hover `bg-shazam-400` → active `bg-shazam-600`
- **Secondary**: `border-gray-700 text-gray-300` → hover `bg-gray-800` → active `bg-gray-700`
- **Danger**: `bg-red-600/20 text-red-300` → hover `bg-red-600/30` → active `bg-red-600/40`
- **Ghost**: `text-gray-400` → hover `bg-gray-800 text-gray-200` → active `bg-gray-700`

### Usage
```vue
<Button variant="primary" @click="save">Save Changes</Button>
<Button variant="danger" size="sm" :loading="isDeleting">Delete</Button>
<Button variant="secondary" size="lg" disabled>Unavailable</Button>
<Button variant="ghost" icon-only aria-label="Close"><XIcon /></Button>
```
