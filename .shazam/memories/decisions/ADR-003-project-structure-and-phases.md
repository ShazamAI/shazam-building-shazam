---
name: adr-003-project-structure-and-phases
description: VS Code extension complete project structure, dependencies, and phased implementation plan
tags: architecture, vscode-extension, decision, planning
---

# ADR-003: VS Code Extension — Project Structure & Phased Implementation Plan

**Status:** Accepted
**Date:** 2026-03-21
**Context:** The Shazam VS Code extension needs a well-defined project structure, dependency list, and incremental implementation plan. Phase 1 scaffolding and Phase 2 core chat are already partially complete.

---

## 1. Complete File/Folder Tree

```
shazam-vscode/
├── package.json                    # Root — scripts, devDependencies
├── tsconfig.json                   # TypeScript config (strict)
├── .eslintrc.json                  # ESLint rules
├── .gitignore                      # ✅ exists
├── LICENSE                         # ✅ exists
│
├── src/                            # Extension host code (Node.js)
│   ├── extension.ts                # ✅ Entry point — activate/deactivate
│   ├── types.ts                    # ✅ Shared type definitions
│   │
│   ├── commands/                   # Command handlers
│   │   ├── registerCommands.ts     # ✅ Central registry
│   │   ├── openChat.ts            # ✅ shazam.openChat
│   │   ├── newConversation.ts     # Phase 3 — shazam.newConversation
│   │   ├── clearHistory.ts        # Phase 3 — shazam.clearHistory
│   │   └── exportChat.ts          # Phase 5 — shazam.exportChat
│   │
│   ├── providers/                  # VS Code providers
│   │   └── ChatViewProvider.ts    # ✅ Webview sidebar provider
│   │
│   ├── services/                   # Business logic (no VS Code API imports)
│   │   ├── ChatService.ts         # Phase 3 — message handling, history
│   │   ├── AiService.ts           # Phase 4 — AI provider abstraction
│   │   └── StorageService.ts      # Phase 3 — persist to globalState
│   │
│   └── utils/                      # Pure utilities
│       ├── getNonce.ts            # ✅ CSP nonce generation
│       ├── markdown.ts            # Phase 3 — markdown-to-HTML rendering
│       └── disposable.ts          # Phase 2 — disposable helpers
│
├── extension/                      # Extension metadata
│   ├── package.json               # ✅ Manifest (contributes, activation)
│   └── icon.png                   # Phase 5 — marketplace icon
│
├── webview/                        # Webview UI source (compiled separately)
│   ├── index.ts                   # Phase 3 — webview entry point
│   ├── styles/
│   │   └── chat.css               # Phase 3 — extracted styles
│   ├── components/
│   │   ├── ChatContainer.ts       # Phase 3 — message list renderer
│   │   ├── MessageInput.ts        # Phase 3 — input area
│   │   └── MessageBubble.ts       # Phase 3 — single message renderer
│   └── lib/
│       └── vscodeApi.ts           # Phase 3 — acquireVsCodeApi wrapper
│
├── dist/                           # Build output (gitignored)
│   ├── extension.js               # Bundled extension host
│   └── webview.js                 # Bundled webview UI
│
└── .vscode/
    └── launch.json                # Debug configuration
```

**Legend:** ✅ = already exists | Phase N = to be created in that phase

---

## 2. npm Dependencies

### Runtime Dependencies

| Package | Version | Purpose | Phase |
|---------|---------|---------|-------|
| `markdown-it` | `^14.0.0` | Render markdown in chat messages (code blocks, links, bold) | 3 |
| `@anthropic-ai/sdk` | `^0.30.0` | Claude API client for AI responses | 4 |

### Dev Dependencies

| Package | Version | Purpose | Phase |
|---------|---------|---------|-------|
| `@types/vscode` | `^1.85.0` | ✅ VS Code API type definitions | 1 |
| `@types/node` | `^20.0.0` | ✅ Node.js type definitions | 1 |
| `typescript` | `^5.3.0` | ✅ TypeScript compiler | 1 |
| `esbuild` | `^0.19.0` | ✅ Fast bundler for extension host | 1 |
| `@types/markdown-it` | `^14.0.0` | Types for markdown-it | 3 |
| `eslint` | `^8.56.0` | Linting | 2 |
| `@typescript-eslint/parser` | `^6.0.0` | TypeScript ESLint parser | 2 |
| `@typescript-eslint/eslint-plugin` | `^6.0.0` | TypeScript ESLint rules | 2 |
| `@vscode/vsce` | `^2.22.0` | Extension packaging for marketplace | 5 |

---

## 3. Extension Manifest Plan (`extension/package.json`)

```jsonc
{
  "name": "shazam",
  "displayName": "Shazam",
  "description": "AI-driven development assistant for VS Code",
  "version": "0.0.1",
  "publisher": "shazam",
  "engines": { "vscode": "^1.85.0" },
  "categories": ["AI", "Chat"],
  "main": "../dist/extension.js",

  // Empty array = activate on any activation event (command, view, etc.)
  "activationEvents": [],

  "contributes": {

    // -- Commands --
    "commands": [
      {
        "command": "shazam.openChat",             // ✅ Phase 1
        "title": "Open Chat",
        "category": "Shazam"
      },
      {
        "command": "shazam.newConversation",       // Phase 3
        "title": "New Conversation",
        "category": "Shazam",
        "icon": "$(add)"
      },
      {
        "command": "shazam.clearHistory",          // Phase 3
        "title": "Clear Chat History",
        "category": "Shazam",
        "icon": "$(trash)"
      },
      {
        "command": "shazam.exportChat",            // Phase 5
        "title": "Export Chat",
        "category": "Shazam",
        "icon": "$(export)"
      }
    ],

    // -- Keybindings --
    "keybindings": [
      {
        "command": "shazam.openChat",              // ✅ Phase 1
        "key": "ctrl+shift+c",
        "mac": "cmd+shift+c"
      }
    ],

    // -- Sidebar --
    "viewsContainers": {
      "activitybar": [
        {
          "id": "shazam-sidebar",                  // ✅ Phase 1
          "title": "Shazam",
          "icon": "$(comment-discussion)"
        }
      ]
    },
    "views": {
      "shazam-sidebar": [
        {
          "type": "webview",                       // ✅ Phase 1
          "id": "shazam.chatView",
          "name": "Chat"
        }
      ]
    },

    // -- View title actions (sidebar header buttons) --
    "menus": {
      "view/title": [
        {
          "command": "shazam.newConversation",      // Phase 3
          "when": "view == shazam.chatView",
          "group": "navigation@1"
        },
        {
          "command": "shazam.clearHistory",         // Phase 3
          "when": "view == shazam.chatView",
          "group": "navigation@2"
        }
      ]
    },

    // -- Extension settings --
    "configuration": {
      "title": "Shazam",
      "properties": {
        "shazam.ai.provider": {                     // Phase 4
          "type": "string",
          "default": "anthropic",
          "enum": ["anthropic"],
          "description": "AI provider for chat responses"
        },
        "shazam.ai.model": {                        // Phase 4
          "type": "string",
          "default": "claude-sonnet-4-20250514",
          "description": "AI model to use for chat"
        },
        "shazam.ai.maxTokens": {                    // Phase 4
          "type": "number",
          "default": 4096,
          "description": "Maximum tokens per AI response"
        }
      }
    }
  }
}
```

---

## 4. Phased Implementation Plan

### Phase 1 — Project Scaffolding ✅ COMPLETE

**Deliverables:**
- [x] Root `package.json` with scripts and devDependencies
- [x] `tsconfig.json` with strict mode
- [x] `extension/package.json` manifest with initial command + view
- [x] `src/extension.ts` entry point
- [x] `src/types.ts` with `ChatMessage` interface
- [x] `src/utils/getNonce.ts`
- [x] esbuild build working (`npm run build`)
- [x] Type-check passing (`npm run type-check`)

**Dependencies:** None
**Complexity:** Low

---

### Phase 2 — Core Extension + Chat Webview ✅ PARTIALLY COMPLETE

**Deliverables:**
- [x] `src/providers/ChatViewProvider.ts` — webview sidebar with inline HTML
- [x] `src/commands/openChat.ts` — open/focus chat command
- [x] `src/commands/registerCommands.ts` — command registry
- [x] Chat UI with message bubbles, input area, send on Enter
- [x] In-memory message history preserved across hide/show (`retainContextWhenHidden`)
- [ ] `.vscode/launch.json` — debug configuration for Extension Host
- [ ] `.eslintrc.json` — linting setup
- [ ] `src/utils/disposable.ts` — disposable cleanup helpers

**Remaining work:**
- Add `.vscode/launch.json` for F5 debugging
- Add ESLint config + devDependencies
- Add disposable utilities for safe cleanup

**Dependencies:** Phase 1
**Complexity:** Medium

---

### Phase 3 — Chat UI Extraction + Persistence + Markdown

**Goal:** Extract inline HTML into proper webview modules, persist messages across sessions, render markdown in responses.

**Deliverables:**
- [ ] `webview/` directory with extracted components (`ChatContainer`, `MessageInput`, `MessageBubble`)
- [ ] `webview/styles/chat.css` — styles extracted from inline HTML
- [ ] `webview/index.ts` — webview entry with `acquireVsCodeApi` wrapper
- [ ] Separate esbuild entry for webview bundle (`dist/webview.js`)
- [ ] `src/services/StorageService.ts` — persist messages via `ExtensionContext.globalState`
- [ ] `src/services/ChatService.ts` — message handling logic extracted from provider
- [ ] `src/utils/markdown.ts` — `markdown-it` rendering for assistant messages
- [ ] `src/commands/newConversation.ts` — reset chat, keep history accessible
- [ ] `src/commands/clearHistory.ts` — delete all persisted messages
- [ ] Update `extension/package.json` — add new commands, menus, view/title actions
- [ ] Update CSP to allow webview stylesheet URI

**Key decisions:**
- `StorageService` uses `globalState.get/update` for persistence (survives restarts)
- `ChatService` owns the message array; `ChatViewProvider` delegates to it
- Webview built as separate esbuild bundle loaded via `webview.asWebviewUri`

**Dependencies:** Phase 2
**Complexity:** High

---

### Phase 4 — AI Integration

**Goal:** Connect chat to Claude API for real AI responses, with streaming and error handling.

**Deliverables:**
- [ ] `src/services/AiService.ts` — Anthropic SDK wrapper
  - `sendMessage(messages: ChatMessage[]): AsyncIterable<string>` for streaming
  - Error handling (rate limits, network, auth)
  - Cancellation support via `AbortController`
- [ ] API key management — prompt user, store in `SecretStorage`
- [ ] Streaming UI — tokens render incrementally in assistant bubble
- [ ] Loading state — typing indicator while waiting for response
- [ ] Stop generation — button to cancel in-flight request
- [ ] Update `extension/package.json` — add `configuration` settings (provider, model, maxTokens)
- [ ] System prompt configuration — default system prompt for code assistant context

**Key decisions:**
- Use `@anthropic-ai/sdk` directly (not a wrapper library)
- API key stored in VS Code `SecretStorage` (encrypted, never in settings)
- Streaming via SDK's `stream()` method, piped to webview via `postMessage`
- Active editor context optionally sent with messages for code-aware responses

**Dependencies:** Phase 3
**Complexity:** High

---

### Phase 5 — Polish & Features

**Goal:** Production readiness, UX refinements, and marketplace preparation.

**Deliverables:**
- [ ] `src/commands/exportChat.ts` — export conversation as Markdown file
- [ ] Code block syntax highlighting in chat (via `highlight.js` or VS Code tokenizer)
- [ ] Copy-to-clipboard button on code blocks
- [ ] "Insert at cursor" action — paste AI code suggestion into active editor
- [ ] Conversation list — multiple conversations with sidebar selector
- [ ] `extension/icon.png` — extension marketplace icon
- [ ] `extension/CHANGELOG.md` — version history
- [ ] Package with `@vscode/vsce` for `.vsix` distribution
- [ ] Keyboard accessibility audit (tab order, screen reader labels)
- [ ] Performance: lazy-load AI service, debounce input, virtualize long conversations

**Dependencies:** Phase 4
**Complexity:** Medium

---

## 5. Phase Dependency Graph

```
Phase 1 (Scaffolding) ✅
    │
    ▼
Phase 2 (Core Extension + Webview) ⏳
    │
    ▼
Phase 3 (UI Extraction + Persistence + Markdown)
    │
    ▼
Phase 4 (AI Integration)
    │
    ▼
Phase 5 (Polish & Features)
```

Each phase is strictly sequential — no phase can begin until the previous is fully complete. Within a phase, tasks can be parallelized across developers.

---

## 6. Decision Rationale

| Decision | Choice | Why |
|----------|--------|-----|
| Bundler | esbuild | Already in use; fastest option for VS Code extensions |
| Webview approach | Separate bundle in `webview/` | Clean separation of Node.js vs browser code; proper CSP |
| State persistence | `globalState` | Built-in, no external deps, survives restarts |
| API key storage | `SecretStorage` | Encrypted by VS Code, never exposed in settings JSON |
| AI SDK | `@anthropic-ai/sdk` | First-class Claude support, streaming, TypeScript types |
| Markdown rendering | `markdown-it` | Lightweight, extensible, widely used in VS Code ecosystem |
| Inline HTML (Phase 2) | Temporary | Quick iteration; extracted to `webview/` in Phase 3 |
