---
id: task_66
title: "Arrume o seguinte bug"
status: completed
assigned_to: manager
created_by: human
company: Shazam
created_at: 2026-03-21T20:43:06.521383Z
completed_at: 2026-03-21T20:43:19.257320Z
updated_at: 2026-03-21T20:43:19.257317Z
---

## Description

Arrume o seguinte bug 
```
[plugin:vite:css] [postcss] /Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/styles/main.css:1:1: The `bg-surface` class does not exist. If `bg-surface` is a custom class, make sure it is defined within a `@layer` directive./Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/styles/main.css:1:01  |  @tailwind base;   |  ^2  |  @tailwind components;3  |  @tailwind utilities;    at Input.error (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/postcss/lib/input.js:135:16)    at AtRule.error (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/postcss/lib/node.js:146:32)    at processApply (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/tailwindcss/lib/lib/expandApplyAtRules.js:380:29)    at /Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/tailwindcss/lib/lib/expandApplyAtRules.js:551:9    at /Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/tailwindcss/lib/processTailwindFeatures.js:55:50    at async plugins (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/tailwindcss/lib/plugin.js:38:17)    at async LazyResult.runAsync (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/postcss/lib/lazy-result.js:293:11)    at async runPostCSS (file:///Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/vite/dist/node/chunks/dep-D4NMHUTW.js:43853:21)    at async compilePostCSS (file:///Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/vite/dist/node/chunks/dep-D4NMHUTW.js:43823:18)    at async compileCSS (file:///Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/node_modules/vite/dist/node/chunks/dep-D4NMHUTW.js:43678:27Click outside, press Esc key, or fix the code to dismiss.You can also disable this overlay by setting server.hmr.overlay to false in vite.config.ts.
```

## Result

**One-line Summary:** Fix missing `bg-surface` Tailwind class causing PostCSS compilation error in dashboard.

```subtasks
[
  {
    "title": "Fix missing bg-surface Tailwind class in dashboard CSS",
    "description": "The dashboard build is failing because the `bg-surface` custom Tailwind class is referenced but not defined. Investigate where `bg-surface` is being used in the CSS and either: (1) define the missing class in Tailwind config with an appropriate color value, or (2) replace it with existing Tailwind classes if the custom class is unnecessary. Ensure the production build compiles without CSS errors.\n\nACs:\n- Dashboard build compiles without PostCSS errors\n- No \"class does not exist\" warnings in console\n- `bg-surface` either defined in Tailwind config or removed from CSS\n- Vite dev server runs without overlay errors",
    "assigned_to": "dashboard_dev_1",
    "depends_on": null
  }
]
```
