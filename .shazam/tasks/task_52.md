---
id: task_52
title: "Implement extracted color scheme in dashboard Tailwind config"
status: completed
assigned_to: dashboard_dev_2
created_by: manager
company: Shazam
created_at: 2026-03-21T20:28:05.941921Z
completed_at: 2026-03-21T20:42:09.153522Z
updated_at: 2026-03-21T20:42:09.153517Z
---

## Description

Update the dashboard's Tailwind CSS configuration to use the color palette extracted from Shazam.dev. Replace all current color tokens with brand colors. Ensure the theme works in both light and dark modes if applicable. Test that the production build succeeds without color-related errors.

ACs:
- Tailwind config updated with Shazam.dev color palette
- All color tokens reference brand colors instead of generic defaults
- Production build compiles successfully
- No console errors related to color definitions

## Result

## Summary

### Changes Made

| File | Change |
|---|---|
| `tailwind.config.js` | Replaced blue-indigo palette with golden-amber palette extracted from Shazam.dev (`#ffca1d` anchor). Added `surface` color tokens (`#0a0a0f`, `#12121a`, `#1a1a24`) from Shazam.dev dark theme. |
| `src/styles/main.css` | Body uses `bg-surface`, cards use `bg-surface-card`, stat cards use `bg-surface-card`. Primary button updated to `bg-shazam-500 text-gray-950` (dark text on golden bg for contrast). Focus ring offset uses `ring-offset-surface`. |
| `src/pages/TasksPage.vue` | All inline golden buttons: `bg-shazam-500 text-gray-950 hover:bg-shazam-400` |
| `src/pages/ConfigPage.vue` | Same button treatment + toggle switches use `bg-shazam-500` |
| `src/pages/SessionsPage.vue` | Retry button updated |
| `src/pages/WorkspacesPage.vue` | Active workspace badge + retry button updated |
| `src/pages/AgentsPage.vue` | Add Agent + form submit buttons updated |
| `src/components/layouts/SidebarNav.vue` | Logo "S" badge: `bg-shazam-500 text-gray-950` |
| `src/components/layouts/MobileSidebar.vue` | Mobile logo badge: same |
| `src/components/features/EventFeed.vue` | Scroll-to-bottom button updated |

### Color Palette (old → new)

| Token | Old (Blue-Indigo) | New (Golden-Amber) |
|---|---|---|
| shazam-50 | `#f0f4ff` | `#fffbeb` |
| shazam-100 | `#dbe4ff` | `#fff3c4` |
| shazam-200 | `#bac8ff` | `#ffe588` |
| shazam-300 | `#91a7ff` | `#ffd54f` |
| shazam-400 | `#748ffc` | `#ffca1d` ← brand primary |
| shazam-500 | `#5c7cfa` | `#f5b800` |
| shazam-600 | `#4c6ef5` | `#d99e00` |
| shazam-700 | `#4263eb` | `#b37d00` |
| shazam-800 | `#3b5bdb` | `#8c6200` |
| shazam-900 | `#364fc7` | `#6b4a00` |

### Key Design Decisions
- **Dark text on golden buttons** (`text-gray-950` instead of `text-white`) — golden backgrounds need dark text for WCAG contrast compliance
- **Surface tokens** from Shazam.dev (`#0a0a0f` body, `#12121a` cards) — darker than Tailwind's `gray-950`/`gray-900`, matching the actual brand
- **Transparent overlays** (`shazam-600/20`, `shazam-700/20`) left unchanged — golden glow effect works beautifully on dark backgrounds

### Build Status
✅ Production build succeeds (938ms). Zero TypeScript errors. Only 1 pre-existing unrelated warning.
