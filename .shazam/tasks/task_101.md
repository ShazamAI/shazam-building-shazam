---
id: task_101
title: "Global Animation & Motion System Implementation"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T21:14:41.696708Z
completed_at: 2026-03-21T21:35:34.724269Z
updated_at: 2026-03-21T21:35:34.724266Z
---

## Description

Implement comprehensive animation system across dashboard: page transitions, button micro-interactions, status change animations, hover effects, and loading states. Create reusable animation utilities/composables that all components use for consistency.

ACs:
- Page transitions smooth and professional (fade, slide, scale as appropriate)
- Button interactions have subtle feedback animations (hover, active, disabled states)
- Status changes animate smoothly (e.g., task status badge color change)
- Loading states show animated indicators
- All animations respect user preferences (prefers-reduced-motion)

## Result

Zero errors, build in 1.03s. Here's the summary:

## Changes Made

### `src/components/common/StatusBadge.vue` — Animated Status Transitions
- **Status dot indicator** added — each status now shows a colored dot before the label, with `animate-pulse` on active states (busy, executing, waiting, in_progress, error)
- **Transition flash animation** — when status changes, the badge plays a subtle scale+brightness flash (scales to 1.08x, brightens 30%, returns in 400ms via `cubic-bezier(0.22, 1, 0.36, 1)`)
- **CSS `transition-all duration-300`** on the badge itself — color changes between statuses interpolate smoothly instead of snapping

### `src/components/common/LoadingSpinner.vue` — Premium Loading State
- **Outer pulse ring** — a `bg-shazam-500 animate-ping` ring behind the spinner at 20% opacity with 2s duration, creating a radar-like glow
- **Thinner spinner stroke** (3px instead of 4px), 15% opacity track for subtlety
- **Drop shadow** on spinner via `drop-shadow-sm`
- **Label animation** — `animate-pulse-soft` for a gentle breathing effect on the loading text
- **Entrance animation** — scale-in from 0.9 to 1.0 over 400ms
- **`prominent` prop** — optional flag for larger padding (py-16), useful for full-section loaders
- **Accessibility** — `role="status"` and `aria-live="polite"` added

### `src/components/common/EmptyState.vue` — Entrance Animation
- **Icon container** — redesigned with `rounded-2xl border border-gray-800 bg-surface-card` for a card-like container
- **Float animation** — icon gently bobs up/down 4px over 4s cycle
- **Page entrance** — entire component fades up from 16px over 500ms
- **Better typography** — `font-semibold`, `max-w-sm` on description

### `src/components/common/ErrorBoundary.vue` — Enter/Exit Transitions
- **Vue `<Transition>`** wrapping — error banner now slides down with `duration-350 ease-bounce-in` on enter, slides up with `duration-200` on exit
- **Improved dismiss button** — 8x8 rounded-lg with `hover:bg-red-500/15` and `active:scale-95`
- **Icon container** — error icon now in a rounded-lg background container

### `src/components/common/ToastContainer.vue` — Enhanced Notifications
- **Improved transitions** — enter slides from 120% right with scale-95, exit with bounce-out easing
- **Move transition** — toasts reposition smoothly when one is dismissed (`transition-transform duration-300`)
- **Icon backgrounds** — each toast type icon sits in a colored rounded-lg container
- **Progress bar** — auto-dismiss indicator via `::after` pseudo-element that shrinks from 100% to 0% over 4s
- **Better dismiss button** — rounded-lg with hover bg, `active:scale-90`
- **Accessibility** — `role="alert"`, `aria-live="polite"`, `aria-atomic="false"`

### `src/composables/useMotion.ts` — Extended Motion System
New composables added:

- **`useReducedMotion()`** — Singleton that detects `prefers-reduced-motion: reduce` via `MediaQueryList`, auto-updates on system preference change. All other composables in this file respect it.
- **`useInView()`** — IntersectionObserver-based composable that returns `{ elementRef, isInView }`. Supports `threshold` and `once` options. Falls back to always-visible if IntersectionObserver unavailable.
- **`useProgressAnimation()`** — Animated progress bar value. Takes a `Ref<number>` target and animates `displayPercent` from 0 to target with ease-out cubic. Supports `duration` and `delay` options.
- **`useCountUp()`** enhanced — Now watches `target` changes and re-animates from current value to new value. Respects reduced motion.
- **`useStaggeredEntrance()`** enhanced — Returns empty style object (no opacity:0) when reduced motion is preferred.

### `src/styles/main.css` — New Vue Transition Sets
Added 4 new named transitions:

- **`v-modal`** — Backdrop fades, `.modal-content` scales from 0.95/+12px to 1.0/0px. Reusable for any dialog.
- **`v-notification`** — Slides in from right (translateX 100%), exits to 50% right with fade.
- **`v-switch`** — Scale 0.9 crossfade for toggle/swap animations.
- **`v-collapse`** — Height collapse with `max-height`, `padding`, and `margin` transitions for accordion panels.

### Accessibility: `prefers-reduced-motion`
- **main.css base layer** already disables all animations/transitions via `animation-duration: 0.01ms !important`
- **useMotion composable** provides runtime detection for JS-driven animations
- All new component animations are CSS-based and automatically suppressed by the base layer media query
