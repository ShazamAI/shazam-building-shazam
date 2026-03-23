---
id: task_102
title: "StatusBar & EventFeed Component Enhancement"
status: completed
assigned_to: dashboard_dev_2
created_by: manager
company: Shazam
created_at: 2026-03-21T21:14:41.696758Z
completed_at: 2026-03-21T21:33:37.336525Z
updated_at: 2026-03-21T21:33:37.336521Z
---

## Description

Redesign StatusBar with improved layout, status indicators, and visual hierarchy. Enhance EventFeed component with better spacing, timeline indicators, event type differentiation, and smooth scrolling effects. Both should feel premium and polished.

ACs:
- StatusBar displays metrics with clear visual separation and professional styling
- Event feed shows events with timeline, type indicators, and clear hierarchy
- Smooth animations when events are added or removed
- Visual distinction between different event types (success, warning, error)
- Professional appearance with proper spacing and typography

## Result

Build passes. Here's the summary:

## Summary: StatusBar & EventFeed Enhancement

### StatusBar — Complete Redesign

**Before:** Flat bar with abbreviated text labels (P:, R:, D:) and minimal styling.

**After:**
- **Company identity section**: Avatar with initial letter, colored by connection status (emerald=connected, red=disconnected), with a live dot indicator and connection status text
- **Status badge**: Rounded pill with dot indicator showing "Live" (pulsing emerald) or "Paused" (yellow), with ring border
- **Metric pills**: Individual bordered containers for each task status (pending, running, done, failed) with colored dots, bold mono counts, and labels on desktop. Failed count only shows when > 0
- **Summary section**: Total tasks and cost displayed in a clean two-column layout (desktop only)
- **Mobile cost**: Compact cost badge visible on small screens
- **Entrance animation**: Smooth `statusBarIn` keyframe sliding down
- **Visual polish**: Gradient background (`from-surface-card to-gray-900/80`), subtle border, metric pill hover states

### EventFeed — Timeline-based Redesign

**Before:** Flat list with colored badges and basic scroll management.

**After:**
- **Header**: Icon in shazam-branded container, section title, error count badge, total event count
- **Timeline**: Each event has a colored dot connected by vertical lines, creating a visual timeline. Dot color matches event category (shazam=output, amber=tool, emerald=success, red=error, cyan=status)
- **Event categorization**: 6 categories (output, tool, success, error, status, system) with consistent color treatment across dots, badges, and accent borders
- **Left accent border**: 2px colored left border per event — streaming events get shazam gold, errors get red, success gets emerald
- **Icon boxes**: 6×6 rounded containers with background tint matching event type
- **Badge styling**: Added ring borders to type badges for better visual weight
- **Streaming cursor**: Custom blink animation (0.8s cubic-bezier) instead of simple pulse
- **Empty state**: Illustrated with icon, title, and subtitle
- **Scroll overlay**: Redesigned as a floating pill button with border, backdrop blur, shazam hover accent, and gradient fade
- **Entrance animations**: `feedIn` for container, `eventSlideIn` for individual events
- **Interactive timeline**: Dots scale up on row hover (`transform: scale(1.3)`)
- **Custom scrollbar**: 4px thin scrollbar matching the dark theme

### Files Changed
- `src/components/features/StatusBar.vue` — Complete rewrite
- `src/components/features/EventFeed.vue` — Complete rewrite

### Build Result
- Zero new TypeScript errors (2 pre-existing in LoadingSpinner.vue)
- Production build: 1.01s, all modules compile cleanly
