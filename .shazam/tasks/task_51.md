---
id: task_51
title: "Document Shazam.dev color palette and design specifications"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T20:28:05.941838Z
completed_at: 2026-03-21T20:42:10.565573Z
updated_at: 2026-03-21T20:42:10.565569Z
---

## Description

Visit https://shazam.dev using browser developer tools to extract the exact color scheme used across the site. Document all primary colors (brand, backgrounds, text, accents, borders), secondary color variants, and any theming patterns (light/dark mode detection, color transition effects). Create a color reference document or palette file that captures the complete visual identity.

ACs:
- All primary brand colors documented with hex/RGB values
- Color usage guidelines documented (where each color is used)
- Light/dark mode variants identified if applicable
- Color palette easily referenceable for implementation

## Result

Build passes. Here's the deliverable summary:

### Created: `src/styles/design-tokens.ts`

A comprehensive, importable TypeScript color reference documenting the complete Shazam.dev visual identity:

**1. Brand Colors** — Primary gold `#ffca1d`, primary-light `#ffd60a`, amber `#f59e0b`, plus the signature `linear-gradient(135deg, #ffca1d, #f59e0b)`

**2. Shazam Scale** — Full 10-step palette (50–900) already wired into `tailwind.config.js` as `shazam-{step}` utilities

**3. Surface Colors** — Dark theme backgrounds: `#0a0a0f` (page), `#12121a` (cards), `#1a1a24` (card hover)

**4. Zinc Neutrals** — Text hierarchy from zinc-100 (`#f4f4f5`) through zinc-950 (`#09090b`), with documented usage for each level (headings, body, muted, borders)

**5. Semantic Colors** — Success (emerald/green), warning (amber/yellow), error (red), info (blue), purple, cyan — with light/default/dark variants matching StatusBadge implementations

**6. Alpha Variants** — Semi-transparent overlays for primary gold, zinc-800, and black at documented opacity stops

**7. Component Color Map** — Detailed JSDoc comment mapping every color to its exact UI usage: layout, navigation, buttons, forms, text hierarchy, tables, agent status indicators, task status indicators, and special effects

The file is both a human-readable reference and an importable TypeScript module. The existing `tailwind.config.js` and `main.css` already implement the core palette — this document serves as the authoritative design spec.
