---
name: project-architecture
description: Architecture patterns, module organization, and design principles
tags: architecture, design, patterns, modules
---

## Architecture Overview

Shazam uses a **Domain-Driven Design (DDD)** approach with two independently deployable domains separated by workspace boundaries. Each domain has its own team of developers supervised by a Project Manager.

## Module Organization

### Dashboard Domain (`shazam-dashboard`)

```
shazam-dashboard/
├── src/                    # TypeScript source files
│   ├── main.ts            # Vue app entry point
│   ├── App.vue            # Root component
│   ├── types/             # TypeScript interfaces & types
│   ├── utils/             # Shared utilities
│   └── api/               # API client & service layer
├── components/            # Vue components
│   ├── common/            # Reusable UI components
│   ├── layouts/           # Layout components
│   └── features/          # Feature-specific components
├── pages/                 # Page-level components
├── composables/           # Vue 3 Composition API composables
├── assets/                # Static assets (images, styles, etc.)
├── vite.config.ts         # Vite configuration
├── tsconfig.json          # TypeScript configuration
└── tailwind.config.ts     # Tailwind CSS configuration
```

**Key Characteristics:**
- Vue 3 Composition API for component logic
- Vite for fast dev and optimized builds
- Tailwind CSS for styling consistency
- TypeScript for type safety
- Modular component architecture

### VS Code Extension Domain (`shazam-vscode`)

```
shazam-vscode/
├── src/                   # Extension main code
│   ├── extension.ts       # Entry point for extension
│   ├── commands/          # Command handlers
│   ├── providers/         # VS Code providers (completions, etc.)
│   └── utils/             # Shared utilities
├── extension/             # VS Code extension metadata
│   ├── package.json       # Extension manifest
│   └── icon.png           # Extension icon
├── webview/               # Webview UI (if used)
├── .vscode/
│   └── launch.json        # Debug configuration
└── tsconfig.json          # TypeScript configuration
```

**Key Characteristics:**
- VS Code Extension API for IDE integration
- TypeScript for type-safe extension development
- Command palette integration
- Language features (diagnostics, completions, etc.)
- Webview for custom UI (when needed)

## Design Patterns

### Component Hierarchy (Dashboard)
- **Page Components**: Top-level components that map to routes
- **Feature Components**: Components that manage a specific feature area
- **Common Components**: Reusable UI components (buttons, modals, etc.)
- **Composables**: Composition API hooks for shared logic

### Service Layer Pattern
- API clients in `src/api/` for external service communication
- Composables for state management and business logic
- Clear separation between presentation and business logic

### Type Safety
- All TypeScript files must have explicit type annotations
- Shared types defined in `src/types/`
- No use of `any` type without explicit justification
- Interface-based design for extensibility

## Dependency Management

### Dashboard
- **Vue 3**: Core framework
- **Vite**: Build and dev server
- **TypeScript**: Language
- **Tailwind CSS**: Styling
- **axios** (typical): HTTP client
- Additional utilities as needed per team

### VS Code Extension
- **VS Code Extension API**: Core platform
- **TypeScript**: Language
- Additional utilities as needed per team

## Module Boundaries

Each domain (dashboard, vscode) is a **self-contained module**:
- Independent git repositories
- Separate teams (PMs and developers)
- Own build and deployment processes
- Own testing and quality assurance
- Minimal cross-domain dependencies

**Communication between domains:**
- Dashboard → VS Code: Via command palette and extension API
- VS Code → Dashboard: Not directly (runs independently)

## Code Organization Principles

1. **Single Responsibility**: Each module has one clear purpose
2. **DRY (Don't Repeat Yourself)**: Common logic in shared utilities
3. **SOLID Principles**: Applied to component and service design
4. **Composition over Inheritance**: Use Vue 3 Composition API
5. **Explicit Dependencies**: All imports are explicit and traceable

See [./conventions.md](./conventions.md) for detailed code style guidelines.
See [../rules/testing.md](../rules/testing.md) for testing architecture.
