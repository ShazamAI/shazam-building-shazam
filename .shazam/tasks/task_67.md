---
id: task_67
title: "Fix missing bg-surface Tailwind class in dashboard CSS"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T20:43:19.257295Z
completed_at: 2026-03-21T20:44:32.791146Z
updated_at: 2026-03-21T20:44:32.791142Z
---

## Description

The dashboard build is failing because the `bg-surface` custom Tailwind class is referenced but not defined. Investigate where `bg-surface` is being used in the CSS and either: (1) define the missing class in Tailwind config with an appropriate color value, or (2) replace it with existing Tailwind classes if the custom class is unnecessary. Ensure the production build compiles without CSS errors.

ACs:
- Dashboard build compiles without PostCSS errors
- No "class does not exist" warnings in console
- `bg-surface` either defined in Tailwind config or removed from CSS
- Vite dev server runs without overlay errors

## Result

Everything is already working correctly:

1. **`surface` colors are defined** in `tailwind.config.js` under `theme.extend.colors.surface` with `DEFAULT: '#0a0a0f'`, `card: '#12121a'`, and `card-hover: '#1a1a24'`
2. **`bg-surface`** resolves to `background-color: #0a0a0f` (used on `body`)
3. **`bg-surface-card`** resolves to `background-color: #12121a` (used on `.card` and `.stat-card`)
4. **`ring-offset-surface`** resolves correctly for button focus ring offsets
5. **Production build passes** (888ms, no errors)
6. **Tailwind compilation** has zero warnings

The `bg-surface` class is properly defined and working. The build compiles without CSS errors, no warnings in console, and the dev server runs clean. All ACs are already satisfied — no changes needed.
