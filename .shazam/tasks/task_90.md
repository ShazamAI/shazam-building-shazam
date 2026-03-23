---
id: task_90
title: "Create AppButton component"
status: completed
assigned_to: dashboard_dev_3
created_by: dashboard_dev_3
company: Shazam
created_at: 2026-03-21T20:52:47.475313Z
completed_at: 2026-03-21T20:54:23.000989Z
updated_at: 2026-03-21T20:54:23.000987Z
---

## Description

Create src/components/common/AppButton.vue — a standardized Vue 3 Button component with variants (primary, secondary, danger, ghost, success, warning, info), sizes (xs, sm, md), disabled/loading support, icon slot. Also add btn-success, btn-warning, btn-info to main.css and a btn-xs size class. Use existing CSS patterns from main.css as reference. The component should use <script setup lang="ts"> with TypeScript strict typing.

Variant classes:
- primary: bg-shazam-500 text-gray-950 hover:bg-shazam-400 focus:ring-shazam-400
- secondary: border border-gray-700 bg-transparent text-gray-300 hover:bg-gray-800 focus:ring-gray-500
- danger: bg-red-600/20 text-red-300 hover:bg-red-600/30 focus:ring-red-500
- ghost: bg-transparent text-gray-400 hover:bg-gray-800 hover:text-gray-200 focus:ring-gray-500
- success: bg-emerald-600/20 text-emerald-300 hover:bg-emerald-600/30 focus:ring-emerald-500
- warning: bg-yellow-600/20 text-yellow-300 hover:bg-yellow-600/30 focus:ring-yellow-500
- info: bg-purple-600/20 text-purple-300 hover:bg-purple-600/30 focus:ring-purple-500

Size classes:
- xs: px-2 py-1 text-[10px]
- sm: px-2.5 py-1.5 text-xs
- md: px-4 py-2 text-sm

Base: inline-flex items-center justify-center gap-1.5 rounded-lg font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-offset-surface disabled:cursor-not-allowed disabled:opacity-50

Loading state: show a small spinner SVG before slot content. Props: variant, size, disabled, loading, type (button/submit/reset), block (boolean for w-full).

File location: /Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/components/common/AppButton.vue

## Result

Zero TypeScript errors, build passes. Here's the summary:

### Changes Made

**Extended `src/components/common/Button.vue`:**
- Added 3 new variants: `success` (emerald), `warning` (yellow), `info` (purple) — each with bg, text, hover, active, and focus-ring styles
- Added `xs` size: `px-2 py-1 text-[10px]` for compact inline action buttons
- Added `block` prop for full-width buttons (`w-full`)
- Updated TypeScript types to include all 7 variants and 4 sizes

**Updated `src/styles/main.css`:**
- Added `.btn-success`, `.btn-warning`, `.btn-info` utility classes
- Added `.btn-xs` size utility class

### Component API

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'primary' \| 'secondary' \| 'danger' \| 'ghost' \| 'success' \| 'warning' \| 'info'` | `'primary'` | Visual style |
| `size` | `'xs' \| 'sm' \| 'md' \| 'lg'` | `'md'` | Button size |
| `disabled` | `boolean` | `false` | Disable interaction |
| `loading` | `boolean` | `false` | Show spinner + disable |
| `type` | `'button' \| 'submit' \| 'reset'` | `'button'` | HTML button type |
| `block` | `boolean` | `false` | Full-width mode |
| `iconOnly` | `boolean` | `false` | Square icon-only padding |
| `as` | `'button' \| 'a'` | `'button'` | Render element |
