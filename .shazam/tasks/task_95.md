---
id: task_95
title: "Design System Foundation & Visual Language"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T21:14:41.696341Z
completed_at: 2026-03-21T21:22:55.202250Z
updated_at: 2026-03-21T21:22:55.202245Z
---

## Description

Establish comprehensive design tokens (colors, spacing, typography, shadows, animations) aligned with billion-dollar startup aesthetic. Create Tailwind config enhancements, document design principles, create animation/motion specifications, and provide component style guide for team reference.

ACs:
- Complete design tokens documented (colors, spacing, typography, shadows, borders, animations)
- Tailwind config updated with new design system tokens and extended animations
- Animation library defined (transitions, micro-interactions, state changes)
- Visual style guide created with examples for all component types
- All team members can reference and implement designs consistently

## Result

Build passes. All files created and updated. Here's the summary:

## Changes Made

### 1. `tailwind.config.js` — Complete Design System Extension

**Colors:**
- Extended `surface` with `raised` and `overlay` for 5-level elevation system
- Added `domain` color map (9 workspace colors) for team identification

**Typography:**
- `fontFamily.sans` → Inter with system fallbacks
- `fontFamily.mono` → JetBrains Mono with fallbacks
- `fontSize['2xs']` → 10px for micro labels
- Custom `letterSpacing.widest-plus` → 0.15em

**Spacing:**
- Added intermediate values (`4.5`, `13`, `15`, `18`, `68`, `76`, `84`, `88`) for sidebar widths and layout gaps

**Shadows — 4-tier elevation system:**
- `elevation-1` through `elevation-4` for layered depth
- `glow-sm/md/lg/xl` — brand gold glow effects
- `glow-success/warning/error/info/purple/cyan` — status-specific glows
- `card` / `card-hover` — card-specific shadow presets
- `inner-glow` / `inner-glow-brand` — top-edge highlights for depth

**Animations (18 named):**
- Entrance: `fade-in`, `fade-in-up/down/left/right`, `scale-in`, `slide-in-bottom/right`
- Exit: `fade-out`, `scale-out`
- Micro-interactions: `shimmer`, `pulse-soft`, `pulse-ring`, `float`, `glow-pulse`, `spin-slow`
- Data: `progress-fill`, `count-up`, `bar-grow`
- Stagger: `stagger-fade-in` (backwards fill for delay support)

**Transitions:**
- Custom durations: `250ms`, `350ms`, `400ms`
- Custom easings: `bounce-in`, `bounce-out`, `smooth`, `spring`

**Background utilities:**
- `brand-gradient`, `brand-gradient-soft`, `surface-gradient`, `hero-glow`, `dot-grid`, `glass`
- Background sizes: `dot-grid`, `dot-grid-lg`

### 2. `src/styles/design-tokens.ts` — Comprehensive Token Documentation

Expanded from color-only reference to complete system:
- **Brand identity** — primary, light, amber + gradients
- **Color scales** — shazam, zinc
- **Surface system** — 5 elevation levels documented
- **Semantic colors** — success, warning, error, info, purple, cyan
- **Domain colors** — 9 workspace colors with base/light/faint variants
- **Alpha variants** — primary, zinc, black transparency scales
- **Elevation system** — shadow definitions for 5 levels
- **Motion system** — easing (4 curves), duration (6 steps), stagger (3 speeds)
- **Typography scale** — families, sizes, weights, tracking
- **Spacing scale** — component, section, card responsive
- **Border radius scale** — sm through full
- **Component color map** — complete reference for layout, nav, buttons, forms, text, status, effects

### 3. `src/styles/main.css` — Enhanced Global Styles

**Base layer:**
- CSS custom properties for motion preference
- `prefers-reduced-motion` full accessibility support
- `::selection` brand-colored text selection
- `:focus-visible` consistent keyboard focus ring
- Firefox scrollbar support via `scrollbar-width/color`

**Component classes (new):**
- `.card-interactive` — hover-responsive card with glow
- `.card-glass` — glass morphism card
- `.badge` / `.badge-dot` — standardized badge pattern
- `.skeleton` — shimmer loading placeholder
- `.page-header` / `.page-title` / `.page-subtitle` — page layout presets
- `.domain-dot-*` — 9 domain color dot classes
- Enhanced `.stat-card` with hover elevation
- Enhanced `.input` with focus glow

**Utility classes (new):**
- `.stagger-1` through `.stagger-8` — animation delay helpers (50ms increments)
- `.text-gradient-brand` — gradient text effect
- `.glow-border-brand` / `.glow-border-brand-hover` — brand glow borders
- `.glass-surface` / `.glass-surface-hover` — glass morphism
- `.bg-dots` / `.bg-dots-lg` — dot grid backgrounds
- `.inner-light` / `.inner-light-brand` — depth highlights
- `.scrollbar-thin` — thin scrollbar variant
- `.transition-smooth/bounce-in/spring` — easing presets

**Vue transitions (7 named):**
- `v-fade` — simple opacity
- `v-fade-up` / `v-fade-down` — directional fade
- `v-scale` — zoom in/out
- `v-slide-right` / `v-slide-bottom` — panel/modal slides
- `v-expand` — accordion collapse
- `v-list` — staggered list with move transitions

### 4. `src/composables/useMotion.ts` — Programmatic Animation Helpers

- `useStaggeredEntrance()` — stagger delay calculator for list items
- `usePageEntrance()` — mount-triggered entrance state
- `useCountUp()` — animated number counter with ease-out cubic
- `MOTION` constant — JS-accessible easing/duration/stagger values matching Tailwind config
