# Shazam CLI -- Complete System Specification

**Version:** 0.9.2
**Runtime:** Elixir/OTP + Rust TUI
**License:** MIT
**Website:** [shazam.dev](https://shazam.dev)

---

## 1. Overview

Shazam is an open-source CLI that orchestrates teams of AI agents from the terminal. The user describes what they want built in natural language. A PM agent breaks it down into subtasks, assigns them to developer agents, and they execute in parallel -- all visible in a real-time TUI dashboard.

### How It Works

1. User types a task in the interactive shell (natural language)
2. The PM agent decomposes it into subtasks with dependencies
3. Subtasks enter the approval queue (or auto-approve if configured)
4. Developer agents execute in parallel, respecting module locks
5. QA agents validate the output
6. User reviews results in the TUI

### Key Design Principles

- **Human-in-the-loop** -- every subtask needs approval before execution (configurable)
- **Fault-tolerant** -- built on OTP with supervision trees, circuit breakers, and auto-retry
- **Provider-agnostic** -- works with Claude Code, Codex, Cursor, Gemini
- **Context-persistent** -- agents learn across tasks via TF-IDF retrieval and auto-extracted learnings
- **Multi-repo** -- agents can work across multiple repositories simultaneously

---

## 2. Architecture

### Tech Stack

| Component | Technology |
|-----------|-----------|
| Runtime | Elixir/OTP (GenServer, ETS, DynamicSupervisor) |
| AI Engine | Claude Code SDK (`claude_code` ~> 0.29) |
| HTTP Server | Bandit (~> 1.0) + Plug (~> 1.16) |
| WebSocket | websock_adapter (~> 0.5) |
| Config Parsing | YamlElixir (~> 2.9) |
| JSON | Jason (~> 1.4) |
| CORS | CorsPlug (~> 3.0) |
| TUI | Rust (ratatui + crossterm) |
| Persistence | JSON files (~/.shazam/) |

### Dependencies (mix.exs)

```elixir
{:claude_code, "~> 0.29"},
{:bandit, "~> 1.0"},
{:plug, "~> 1.16"},
{:jason, "~> 1.4"},
{:cors_plug, "~> 3.0"},
{:websock_adapter, "~> 0.5"},
{:yaml_elixir, "~> 2.9"}
```

### OTP Supervision Tree

```
Shazam.Supervisor (one_for_one)
  |- Registry (CompanyRegistry)          -- unique names for Company GenServers
  |- Registry (RalphLoopRegistry)        -- unique names for RalphLoop GenServers
  |- DynamicSupervisor (AgentSupervisor)
  |- DynamicSupervisor (CompanySupervisor)
  |- DynamicSupervisor (RalphLoopSupervisor)
  |- Shazam.TaskBoard (GenServer, ETS-backed)
  |- Shazam.SessionPool (GenServer)
  |- Shazam.API.EventBus (GenServer)
  |- Shazam.Metrics (GenServer)
  |- Shazam.AgentInbox (GenServer)
  |- Shazam.AgentPulse (GenServer)
  |- Shazam.ContextManager (GenServer)
  |- Shazam.CircuitBreaker (GenServer)
  |- Shazam.PluginManager (GenServer)
  +- Bandit HTTP Server (port 4040)
```

Defined in `lib/shazam/application.ex`. The supervisor uses `one_for_one` strategy. Companies and RalphLoops are started dynamically via their respective DynamicSupervisors when the user runs `/start`.

### Data Flow: Task Execution

```
RalphLoop polls TaskBoard every 5s
  -> TaskScheduler selects best pending task + agent
    -> TaskExecutor builds prompt (memory, skills, role rules, context)
      -> PluginManager.before_query() -- plugins can mutate prompt
        -> SessionPool.checkout() gets reused Claude session
          -> Orchestrator.execute_on_session() runs on Claude/Codex/Cursor/Gemini
        -> PluginManager.after_query() -- plugins can mutate result
          -> SubtaskParser extracts subtasks from output
            -> PluginManager.after_task_complete() -- plugins notified
              -> TaskBoard.complete() marks original task done
                -> ContextManager.capture() stores context for future tasks
```

---

## 3. Core Modules

### 3.1 Entry Points

#### `Shazam` (`lib/shazam.ex`)
Public API module. Key functions:
- `run/2` -- parallel execution of multiple agents
- `pipeline/2` -- sequential pipeline execution
- `start_company/1` -- start a company from a config map
- `assign/3` -- assign a task to an agent in a company

#### `Shazam.Application` (`lib/shazam/application.ex`)
OTP application bootstrap. Starts the supervision tree, initializes `Store`, restores workspace path from disk. Does NOT auto-restore companies (to avoid RalphLoop race conditions).

#### `Shazam.CLI` (`lib/shazam/cli.ex`)
Escript main_module. Parses CLI arguments and dispatches to subcommands: `init`, `start`, `shell`, `status`, `stop`, `task`, `org`, `logs`, `agent add`, `apply`, `dashboard`, `version`, `update`, `help`.

### 3.2 Company & Organization

#### `Shazam.Company` (`lib/shazam/company.ex`)
GenServer managing an agent hierarchy, tasks, and org chart. Registered via `Shazam.CompanyRegistry`. Each company has a name, mission, domain config, and list of agents.

#### `Shazam.Company.Builder` (`lib/shazam/company/builder.ex`)
Agent config building and persistence. Constructs `AgentWorker` structs from YAML config, resolves presets, applies domain restrictions.

#### `Shazam.AgentWorker` (`lib/shazam/agent_worker.ex`)
Struct representing an agent's configuration. Implements the `Access` behaviour so `agent[:name]` works.

```elixir
defstruct [
  :name,              # String -- unique identifier
  :role,              # String -- "Senior Developer", "Project Manager", etc.
  :supervisor,        # String | nil -- name of supervising agent
  :domain,            # String | nil -- restricts file access
  :system_prompt,     # String | nil -- custom system prompt
  :model,             # String | nil -- AI model override
  :fallback_model,    # String | nil -- fallback model
  :provider,          # String | nil -- "claude_code", "codex", "cursor", "gemini"
  tools: [],          # [String] -- allowed tools
  skills: [],         # [String] -- skill references
  modules: [],        # [String] -- file path restrictions
  budget: 100_000,    # integer -- token budget
  tokens_used: 0,     # integer -- cumulative tokens
  heartbeat_interval: 60_000,
  status: :idle,      # :idle | :busy | :paused
  context: %{},       # map -- runtime context
  task_history: [],    # [String] -- completed task IDs
  company_ref: nil     # reference to parent company
]
```

#### `Shazam.AgentPresets` (`lib/shazam/agent_presets.ex`)
11 pre-configured role templates: `pm`, `senior_dev`, `junior_dev`, `qa`, `designer`, `researcher`, `devops`, `writer`, `market_analyst`, `competitor_analyst`, `pr_reviewer`.

#### `Shazam.Hierarchy` (`lib/shazam/hierarchy.ex`)
Org chart operations. Uses Kahn's algorithm for topological sort and cycle detection. Builds supervisor-to-reports relationships.

#### `Shazam.ModuleManager` (`lib/shazam/module_manager.ex`)
File-level access control. Prevents concurrent edits to the same file by multiple agents. Locks modules when an agent checks out a task.

### 3.3 Task System

#### `Shazam.TaskBoard` (`lib/shazam/task_board.ex`)
ETS-backed task CRUD with atomic checkout. Central task registry. Persists to disk via `Shazam.Store` with 1-second debounce.

Task type:
```elixir
@type task :: %{
  id: String.t(),                  # "task_1", "task_2", etc.
  title: String.t(),
  description: String.t() | nil,
  status: :pending | :in_progress | :completed | :failed | :awaiting_approval | :paused,
  assigned_to: String.t() | nil,   # agent name
  created_by: String.t() | nil,    # "human" or agent name
  parent_task_id: String.t() | nil,
  depends_on: String.t() | nil,    # title of dependency task
  company: String.t() | nil,
  result: any(),
  created_at: DateTime.t(),
  updated_at: DateTime.t()
}
```

Key functions:
- `create/1` -- creates a task, returns ID
- `checkout/2` -- atomic checkout (pending -> in_progress)
- `complete/2` -- marks completed with result
- `fail/2` -- marks failed with reason
- `list/1` -- list with filters (:status, :assigned_to)
- `create_awaiting/1` -- creates with :awaiting_approval status
- `approve/1` -- awaiting_approval -> pending
- `reject/1` -- awaiting_approval -> rejected
- `pause/1`, `resume/1` -- pause/resume tasks
- `delete/1` -- soft delete
- `purge/1` -- hard delete

#### `Shazam.TaskBoard.Persistence` (`lib/shazam/task_board/persistence.ex`)
ETS-to-disk serialization logic extracted from TaskBoard.

#### `Shazam.TaskScheduler` (`lib/shazam/task_scheduler.ex`)
Task selection algorithm. Given pending tasks and available agents, picks the best match considering:
- Agent availability (not busy)
- Module lock conflicts
- Peer reassignment (idle agents pick up tasks from busy peers)
- Dependency resolution (tasks with `depends_on` wait for parent completion)
- Task deduplication (same title + agent + company in pending/in_progress)

#### `Shazam.TaskExecutor` (`lib/shazam/task_executor.ex`)
Prompt building and execution. Assembles the full prompt from:
1. Agent system prompt (config file > `.shazam/agents/<name>.md` > hardcoded preset)
2. Skills prompt
3. Modules/domain restriction prompt
4. Memory/skill memory prompt
5. PM-specific instructions (if PM role)
6. Implementation instructions (if non-PM role)
7. Role rules (dev cannot write tests, QA cannot implement features, etc.)
8. Tech stack prompt
9. Agent query instructions
10. Context from ContextManager (learnings, recent work, team activity, TF-IDF)
11. Git context (branch, status, modified files)

Timeout: 30 minutes (`@task_timeout 1_800_000`).

#### `Shazam.TaskExecutor.PromptBuilder` (`lib/shazam/task_executor/prompt_builder.ex`)
All prompt-building functions extracted from TaskExecutor. Includes:
- `build_skills_prompt/1`
- `build_modules_prompt/1`
- `build_pm_prompt/1` -- PM-specific delegation instructions
- `build_designer_context/1`
- `build_analyst_context/1`
- `build_role_rules/1` -- role separation enforcement
- `build_domain_restriction_prompt/2`
- `build_tech_stack_prompt/0`
- `implementation_instructions/0` -- forces non-PM agents to implement, not plan

#### `Shazam.SubtaskParser` (`lib/shazam/subtask_parser.ex`)
Extracts subtasks from agent output. When a PM outputs a JSON array inside a `subtasks` code block, this parser creates child tasks.

Expected format:
```json
[
  {"title": "...", "description": "...", "assigned_to": "senior_dev", "depends_on": null},
  {"title": "...", "assigned_to": "qa", "depends_on": "Implement auth middleware"}
]
```

#### `Shazam.TaskTemplates` (`lib/shazam/task_templates.ex`)
Pre-built prompt templates for common task types.

#### `Shazam.RetryPolicy` (`lib/shazam/retry_policy.ex`)
Exponential backoff for failed tasks. Delays: 5s, 15s, 30s. Max retries configurable (default: 2).

#### `Shazam.TaskFiles` (`lib/shazam/task_files.ex`)
Markdown file persistence for tasks. Each task is a `.md` file in `.shazam/tasks/` with YAML frontmatter. Supports bulk create, import (`/tasks --sync`), and export (`/tasks --export`).

### 3.4 Execution Loop

#### `Shazam.RalphLoop` (`lib/shazam/ralph_loop.ex`)
Per-company task execution loop. Registered via `Shazam.RalphLoopRegistry`. The heart of the system.

State struct:
```elixir
defstruct [
  max_concurrent: 4,       # max parallel agent executions
  running: %{},            # %{task_id => %{pid: pid, ref: ref, agent_name: ...}}
  company_name: nil,
  paused: false,
  auto_approve: false,     # human-in-the-loop
  module_lock: true,
  peer_reassign: true,
  poll_interval: 5_000,    # ms
  auto_retry: true,
  max_retries: 2,
  memory_warn_mb: 500,
  status: :idle
]
```

Delegates to:
- `TaskScheduler` -- task selection
- `TaskExecutor` -- prompt building and execution
- `SubtaskParser` -- parsing subtasks from output
- `ModuleManager` -- module auto-claim
- `RetryPolicy` -- retry decisions

### 3.5 Session & Persistence

#### `Shazam.SessionPool` (`lib/shazam/session_pool.ex`)
Reusable Claude Code session management. One session per agent. Sessions recycled after:
- 8 tasks (`@max_tasks_before_reset`)
- 15 minutes idle (`@idle_timeout`)
- Structural config change (model, tools, cwd, permission_mode, timeout)

State: `%{agent_name => %{pid: pid, last_used: DateTime, struct_hash: hash, task_count: int}}`

Structural keys (force session recreation): `[:model, :allowed_tools, :cwd, :add_dir, :permission_mode, :timeout]`

Key functions:
- `checkout/2` -- returns `{:ok, pid, :new | :reused}`
- `checkin/1` -- marks session as idle
- `kill/1` -- kills specific session
- `kill_all/0` -- kills all sessions

#### `Shazam.Orchestrator` (`lib/shazam/orchestrator.ex`)
Parallel/pipeline agent execution engine. Interfaces with Claude Code SDK. Handles session-based and stateless execution modes.

#### `Shazam.Store` (`lib/shazam/store.ex`)
JSON file persistence layer. Stores data in `~/.shazam/` as JSON files.

```elixir
# API
Store.init()                  # creates ~/.shazam/ directory
Store.save(key, data)         # saves as ~/.shazam/{key}.json
Store.load(key)               # returns {:ok, data} | {:error, :not_found}
Store.list_keys(prefix)       # lists keys matching prefix
Store.delete(key)             # removes key
```

Key naming convention:
- `"workspace"` -- current workspace path
- `"company:{name}"` -- company config
- `"tasks:{company}"` -- task board per company

### 3.6 Context & Intelligence

#### `Shazam.ContextManager` (`lib/shazam/context_manager.ex`)
Cross-provider context persistence with atomized topic files. Works with ALL providers (not just Claude).

Constants:
- `@default_history 5` -- last N tasks per agent
- `@default_team_activity 10` -- last N team tasks
- `@default_budget 4_000` -- max chars injected into prompt
- `@max_team_entries 200` -- auto-trim threshold
- `@max_topic_lines 100` -- per-topic file limit

Key functions:
- `capture/4` -- fire-and-forget capture of completed task context
- `build_context/2` -- builds context string for agent's next task

What agents receive in their prompt:
```
## What You Know
- Project uses Vue.js
- API uses JWT with 1h expiration via jose library

## Your Recent Work
### [2026-03-20 12:00] Fix JWT expiration
  Changed token TTL...

## Recent Team Activity
### [2026-03-20 11:55] pm: Plan auth system
  Delegated 3 subtasks...

## Related Context
### [2026-03-20 11:30] senior_2: Setup database
  Created users table...
```

Storage structure:
```
.shazam/context/
  agents/
    senior_1/
      index.md              # auto-generated links + key learnings
      _learnings.md         # decisions, discoveries, patterns (deduped)
      implement_jwt_auth.md # topic file
      build_rest_api.md     # topic file
    pm/
      index.md
      _learnings.md
  team_activity.md          # chronological log (auto-trimmed at 200 entries)
```

Auto-extraction: regex patterns detect decisions, discoveries, tech stack, warnings from agent output. Deduplication via Jaccard similarity (>0.7 = already known).

#### `Shazam.ContextRAG` (`lib/shazam/context_rag.ex`)
TF-IDF retrieval engine. Pure Elixir, zero dependencies.

Algorithm:
1. Tokenize -- split into lowercase words, remove stopwords
2. TF (Term Frequency) -- augmented TF with partial match bonus
3. IDF (Inverse Document Frequency) -- rarity across all chunks
4. Score -- TF * IDF per query term, summed per chunk
5. Rank -- return top-K chunks by score

Indexes `.md` files from: `.shazam/context/`, `.shazam/tasks/`, `.shazam/memories/`

```elixir
ContextRAG.search("JWT authentication bug", top_k: 5)
# => [{0.85, "### [2026-03-20] senior_1: Implement JWT auth\n  Created /lib/jwt.ts..."}]
```

Stopwords include common English words plus domain terms: `task`, `agent`, `create`, `implement`, `build`, `add`, `fix`, `update`, `use`, `make`.

#### `Shazam.GitContext` (`lib/shazam/git_context.ex`)
Git-awareness via `System.cmd("git", ...)`. Zero dependencies. Injects into prompts:
- Current branch name
- Working tree status (clean/dirty, modified files)
- Recent commits
- Modified files list

Auto-injected for new sessions and stateless providers.

#### `Shazam.AgentQuery` (`lib/shazam/agent_query.ex`)
Agent-to-agent knowledge sharing. Passive lookup (does NOT execute the other agent).

Agents output: `AGENT_QUERY: senior_2 What is the users table schema?`

System reads target agent's learnings and topic files, injects response inline. Max 2 queries per task (prevents loops).

#### `Shazam.AgentPulse` (`lib/shazam/agent_pulse.ex`)
Real-time activity sparkline per agent. Characters: `\u2581\u2582\u2583\u2584\u2585\u2586\u2587\u2588` based on events/second. Stall detection: warning when no events for >30 seconds. Sent to TUI via status JSON.

#### `Shazam.ProjectDetector` (`lib/shazam/project_detector.ex`)
Auto-detects tech stack from project files: `package.json`, `mix.exs`, `Cargo.toml`, `go.mod`, etc. Detects framework, language, database, styling, testing, package manager, CI/CD. Suggests agents and domains for `shazam init`.

### 3.7 Provider System

#### `Shazam.Provider` (`lib/shazam/provider.ex`)
Behaviour with 6 callbacks:

```elixir
@callback start_session(session_opts()) :: {:ok, session()} | {:error, any()}
@callback stop_session(session()) :: :ok
@callback execute(session(), String.t(), keyword()) :: result()
@callback supports_sessions?() :: boolean()
@callback name() :: String.t()
@callback available?() :: boolean()
```

| Provider | Module | CLI Binary | Sessions |
|----------|--------|-----------|----------|
| `claude_code` | `Shazam.Provider.ClaudeCode` | `claude` | Yes |
| `codex` | `Shazam.Provider.Codex` | `codex` | No |
| `cursor` | `Shazam.Provider.Cursor` | `cursor` | No |
| `gemini` | `Shazam.Provider.Gemini` | `gemini` | No |

#### `Shazam.Provider.ClaudeCode` (`lib/shazam/provider/claude_code.ex`)
Claude Code provider with full session support via SessionPool.

#### `Shazam.Provider.Codex` (`lib/shazam/provider/codex.ex`)
OpenAI Codex CLI provider. Stateless (no session reuse).

#### `Shazam.Provider.Cursor` (`lib/shazam/provider/cursor.ex`)
Cursor CLI provider. Stateless.

#### `Shazam.Provider.Gemini` (`lib/shazam/provider/gemini.ex`)
Google Gemini CLI provider. Stateless.

#### `Shazam.Provider.Resolver` (`lib/shazam/provider/resolver.ex`)
Maps provider name strings to modules:
- `"claude_code"` -> `Shazam.Provider.ClaudeCode`
- `"codex"` -> `Shazam.Provider.Codex`
- `"cursor"` -> `Shazam.Provider.Cursor`
- `"gemini"` -> `Shazam.Provider.Gemini`

### 3.8 Plugin System

#### `Shazam.Plugin` (`lib/shazam/plugin.ex`)
Behaviour with 8 optional callbacks:

```elixir
@callback on_init(context()) :: :ok | {:error, term()}
@callback before_task_create(attrs :: map(), context()) :: {:ok, map()} | {:halt, term()}
@callback after_task_create(task :: map(), context()) :: {:ok, map()}
@callback before_task_complete(task_id :: String.t(), result :: term(), context()) :: {:ok, term()} | {:halt, term()}
@callback after_task_complete(task_id :: String.t(), result :: term(), context()) :: {:ok, term()}
@callback before_query(prompt :: String.t(), agent_name :: String.t(), context()) :: {:ok, String.t()} | {:halt, term()}
@callback after_query(result :: term(), agent_name :: String.t(), context()) :: {:ok, term()}
@callback on_tool_use(tool_name :: String.t(), input :: map(), agent_name :: String.t(), context()) :: :ok
```

Context type:
```elixir
@type context :: %{
  company_name: String.t(),
  agents: [map()],
  tasks: [map()],
  plugin_config: map()
}
```

`use Shazam.Plugin` adds the behaviour. All callbacks are optional.

Return values:
- `{:ok, data}` -- continue pipeline with (possibly modified) data
- `{:halt, reason}` -- stop pipeline, cancel operation (before events only)
- `:ok` -- continue (for on_init, on_tool_use)

#### `Shazam.PluginManager` (`lib/shazam/plugin_manager.ex`)
GenServer that loads plugins, runs event pipelines. Zero-cost when no plugins loaded (uses `persistent_term` fast path). Plugins execute in alphabetical filename order.

Pipeline: data flows through plugins sequentially. "before" hooks can halt; "after" hooks can mutate output.

#### `Shazam.PluginLoader` (`lib/shazam/plugin_loader.ex`)
Runtime compilation of `.shazam/plugins/*.ex` files. Hot-reload via `/plugins reload`.

### 3.9 Resilience

#### `Shazam.CircuitBreaker` (`lib/shazam/circuit_breaker.ex`)
Auto-pauses RalphLoop after 3 consecutive task failures (`@default_threshold 3`).

State:
```elixir
%{
  consecutive_failures: 0,
  last_error: nil,
  tripped: false,
  threshold: 3
}
```

Key functions:
- `record_failure/1` -- increments counter, trips if threshold reached
- `record_success/0` -- resets counter
- `tripped?/0` -- check if open
- `reset/0` -- manual reset
- `status/0` -- get full state

On trip: broadcasts event, logs to FileLogger, auto-pauses all RalphLoops via Registry. Reset manually with `/resume`.

### 3.10 Memory Systems

#### `Shazam.MemoryBank` (`lib/shazam/memory_bank.ex`)
Legacy per-agent markdown files at `.shazam/memory/{agent_name}.md`. Capped at ~8,000 characters. Contains: Project Overview, Architecture & Patterns, Agent Responsibilities, Lessons Learned, Dependencies.

#### `Shazam.SkillMemory` (`lib/shazam/skill_memory.ex`)
Structured skill-graph system at `.shazam/memories/` with YAML frontmatter:

```
.shazam/memories/
  |- SKILL.md              # Root skill index
  |- project/
  |    |- overview.md
  |    |- architecture.md
  |    +- conventions.md
  |- agents/
  |    |- pm.md
  |    +- senior_dev.md
  |- rules/
  |    |- testing.md
  |    +- git-workflow.md
  +- decisions/
       +- 001-auth-strategy.md
```

Each skill file format:
```markdown
---
name: skill-name
description: One line description
tags: tag1, tag2
---
Content here. Reference other skills: [./rules/testing.md](./rules/testing.md)
```

### 3.11 API & Events

#### `Shazam.API.Router` (`lib/shazam/api/router.ex`)
REST API endpoint handlers via Plug.

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/api/companies` | List all running companies |
| `POST` | `/api/companies` | Create a new company |
| `GET` | `/api/companies/:name/agents` | List agents in a company |
| `PUT` | `/api/companies/:name/agents` | Update agents configuration |
| `GET` | `/api/companies/:name/org-chart` | Get organizational chart |
| `POST` | `/api/companies/:name/tasks` | Create a task in a company |
| `GET` | `/api/tasks` | List tasks (supports filters) |
| `GET` | `/api/events/recent` | Get recent events |

Routes are split into focused modules:
- `Shazam.API.Routes.CompanyRoutes` (`lib/shazam/api/routes/company_routes.ex`)
- `Shazam.API.Routes.TaskRoutes` (`lib/shazam/api/routes/task_routes.ex`)
- `Shazam.API.Routes.RalphRoutes` (`lib/shazam/api/routes/ralph_routes.ex`)
- `Shazam.API.Routes.SkillRoutes` (`lib/shazam/api/routes/skill_routes.ex`)
- `Shazam.API.Routes.WorkspaceRoutes` (`lib/shazam/api/routes/workspace_routes.ex`)
- `Shazam.API.Routes.MiscRoutes` (`lib/shazam/api/routes/misc_routes.ex`)
- `Shazam.API.Helpers` (`lib/shazam/api/helpers.ex`)

#### `Shazam.API.EventBus` (`lib/shazam/api/event_bus.ex`)
Real-time event pub/sub broadcasting. Subscribers (WebSocket, TUI) receive:
- Agent text output (deltas and complete)
- Tool usage notifications
- Task status changes
- System events (circuit breaker, health warnings)

#### `Shazam.API.WebSocket` (`lib/shazam/api/websocket.ex`)
WebSocket at `ws://localhost:4040/ws`. Streams events in real-time.

### 3.12 CLI & TUI

#### `Shazam.CLI.REPL` (`lib/shazam/cli/repl.ex`)
Interactive shell with command history. Minimal module (~99 lines after refactor). Delegates to TuiPort for actual rendering.

#### `Shazam.CLI.YamlParser` (`lib/shazam/cli/yaml_parser.ex`)
Parses and validates `shazam.yaml`. Handles nested tech_stack, workspace configs, plugin configs.

#### `Shazam.CLI.Formatter` (`lib/shazam/cli/formatter.ex`)
Terminal output formatting -- colors, tables, org chart rendering.

#### `Shazam.CLI.TuiPort` (`lib/shazam/cli/tui_port.ex`)
Communicates with the Rust `shazam-tui` binary via Erlang Port. Elixir sends JSON render commands; Rust sends user input events.

Protocol: JSON over fd 3/4 (not stdin/stdout -- Rust uses stdin/stdout for terminal rendering via ratatui). Uses `:nouse_stdio` port option.

Delegates to:
- `Shazam.CLI.TuiPort.Commands` -- command router
- `Shazam.CLI.TuiPort.Helpers` -- utility functions
- `Shazam.CLI.TuiPort.Status` -- status bar data

#### `Shazam.CLI.TuiPort.Commands` (`lib/shazam/cli/tui_port/commands.ex`)
Command router, split into focused modules:
- `Commands.System` -- `/start`, `/stop`, `/resume`, `/help`, `/config`, `/health`, `/clear`, `/quit`
- `Commands.Tasks` -- `/task`, `/approve`, `/reject`, `/kill-task`, `/pause-task`, `/resume-task`, `/retry-task`, `/delete-task`, `/search`, `/export`, `/aa`
- `Commands.Agents` -- `/agent add`, `/agent edit`, `/agent remove`, `/team create`, `/org`, `/agents`, `/dashboard`, `/workspaces`, `/msg`
- `Commands.Review` -- `/review` with all sub-flags (`--learn`, `--post`, `--check`, `--resolve`, `--patterns`)
- `Commands.Tools` -- `/plan`, `/qa`, `/memory-bank`, `/plugins`

#### TUI Binary (Rust)
Located at `shazam-tui/`. Built with `cargo build --release`. Uses ratatui + crossterm.

Features:
- Scrollable event feed with scrollbar and mouse support
- Overlays: tasks, agents, config, dashboard
- Ghost text autocomplete for `/commands`
- Task action menu (Enter on task -> approve/reject/pause/kill/retry)
- Inline task actions: `a` approve, `r` reject, `p` pause, `x` kill
- Markdown rendering in task detail view (headers, lists, checkboxes, code blocks, tables)
- Status bar: sparklines, cost, git branch, provider, memory

Status bar format:
```
Petgenda | running | pm:...sr1:... | P:3 R:2 D:5 | $0.42 | main OK | 63MB
```

Keybindings:
- Up/Down -- command history / navigate lists
- Tab -- accept ghost text autocomplete
- PgUp/PgDn -- scroll events
- Enter -- task detail view (in /tasks), action menu
- `a` -- approve (in task overlay)
- `r` -- reject (in task overlay)
- `p` -- pause (in task overlay)
- `x` -- kill (in task overlay)
- ESC -- close overlay
- Ctrl+C -- exit
- Mouse scroll -- scroll events

Command history saved at `~/.shazam/tui_history`.

### 3.13 Other Modules

#### `Shazam.Metrics` (`lib/shazam/metrics.ex`)
Token usage and performance tracking. ETS-backed, in-memory. Tracks per-agent token usage and cost ($). Persisted across sessions.

#### `Shazam.FileLogger` (`lib/shazam/file_logger.ex`)
File-based logging to disk.

#### `Shazam.AgentInbox` (`lib/shazam/agent_inbox.ex`)
Per-agent message queue for terminal input. `/msg <agent> <message>` sends messages.

#### `Shazam.AgentConfig` (`lib/shazam/agent_config.ex`)
Manages editable agent config files (`.shazam/agents/*.md`).

#### `Shazam.PRReviewer` (`lib/shazam/pr_reviewer.ex`)
PR review agent. Reviews PRs with full codebase context using Opus 4.6. Supports learning from team review patterns.

#### `Shazam.PlanManager` (`lib/shazam/plan_manager.ex`)
Phased execution planning. Plans saved in `.shazam/plans/`.

#### `Shazam.QAManager` (`lib/shazam/qa_manager.ex`)
Automated QA checklists with test cases. QA docs saved in `.shazam/qa/`.

---

## 4. Task Lifecycle

```
                    +-----------------+
                    | awaiting_approval|
                    +--------+--------+
                     approve |  | reject
                             v  v
pending --> in_progress --> completed
                |
                +--> failed --> retry (exponential backoff) --> pending
                |
                +--> paused --> resume --> pending
```

### States

| State | Description |
|-------|-------------|
| `pending` | Waiting to be picked up by an agent |
| `in_progress` | Agent is actively working on it |
| `completed` | Done, result stored |
| `failed` | Execution failed, may retry |
| `awaiting_approval` | PM-created subtask waiting for human approval |
| `paused` | Manually paused by user |

### Transitions

- **Create** -> `pending` (or `awaiting_approval` if from PM with auto_approve=false)
- **Checkout** -> `pending` to `in_progress` (atomic via ETS)
- **Complete** -> `in_progress` to `completed`
- **Fail** -> `in_progress` to `failed`
- **Retry** -> `failed` to `pending` (with retry count increment, exponential backoff)
- **Approve** -> `awaiting_approval` to `pending`
- **Reject** -> `awaiting_approval` to `rejected`
- **Pause** -> any to `paused`
- **Resume** -> `paused` to `pending`
- **Delete** -> soft delete (removes from board, keeps file)
- **Purge** -> hard delete (removes file too)

### Retry Policy

Exponential backoff delays: 5s, 15s, 30s. Default max retries: 2. Configurable via `max_retries` in shazam.yaml.

### Task Counter Isolation

Each workspace starts from `task_1`. No cross-project counter inflation.

### Task Deduplication

Prevents duplicate tasks with same title + agent + company in pending/in_progress states.

---

## 5. Agent Hierarchy

### Standard Single-Team

```
CEO (you) --> PM (Haiku 4.5)
                |
     +----------+----------+
     |          |          |
  Dev Sr.    Dev Jr.    QA
  (Opus)    (Sonnet)  (Sonnet)
```

### Multi-Team with Engineering Manager

```
CEO (you) --> Engineering Manager (Haiku 4.5)
                |
     +----------+----------+
     |                     |
  PM Dashboard          PM VS Code
  (Haiku 4.5)          (Haiku 4.5)
     |                     |
  +--+--+               +--+--+
  |  |  |               |  |  |
  D1 D2 D3              D1 D2 D3
```

The Engineering Manager receives all tasks and delegates to the correct PM based on context. Only needed with multiple teams.

### Role Separation

| Role | Can Do | Cannot Do |
|------|--------|-----------|
| **PM/Manager** | Delegate, break down tasks, coordinate | Read code, use dev tools, implement |
| **Dev** | Implement features, fix bugs, refactor | Write tests |
| **QA** | Write tests, report bugs, validate | Implement features |
| **Analyst** | Research, analyze data, report | Write code |

### Agent Presets

| Preset | Role | Default Model |
|--------|------|--------------|
| `pm` | Project Manager | Haiku 4.5 |
| `senior_dev` | Senior Developer | Opus/Sonnet |
| `junior_dev` | Junior Developer | Sonnet |
| `qa` | QA Engineer | Sonnet |
| `designer` | UI Designer | Sonnet |
| `researcher` | Researcher | Sonnet |
| `devops` | DevOps Engineer | Sonnet |
| `writer` | Technical Writer | Sonnet |
| `market_analyst` | Market Analyst | Sonnet |
| `competitor_analyst` | Competitor Analyst | Sonnet |
| `pr_reviewer` | PR Reviewer | Opus 4.6 |

### Default Tool Sets by Role

| Role | Default Tools |
|------|--------------|
| Manager/PM | Read, Grep, Glob, WebSearch |
| Developer | Read, Edit, Write, Bash, Grep, Glob |
| QA | Read, Edit, Write, Bash, Grep, Glob |
| Analyst | Read, Grep, WebSearch, WebFetch |

---

## 6. Context System

### ContextManager Flow

1. Agent completes a task
2. `ContextManager.capture/4` extracts a summary + routes it to a topic file
3. Learnings auto-extracted (decisions, discoveries, tech stack, warnings)
4. Next task: `ContextManager.build_context/2` returns agent's history + team activity + TF-IDF relevant context

### Learnings Extraction

Regex patterns detect:
- Decisions ("decided to...", "chose...", "switched to...")
- Discoveries ("found that...", "discovered...")
- Tech stack (file path analysis detects frameworks: Vue, React, Supabase, etc.)
- Warnings ("watch out for...", "be careful with...")

Deduplication: Jaccard similarity >0.7 = already known, skip.

### Topic File Routing

New context entries are routed to existing topic files by keyword overlap. If no match exceeds the minimum similarity threshold (`@min_similarity 2`), a new topic file is created.

### ContextRAG Integration

When building context for the next task, ContextRAG performs TF-IDF search across all `.md` files in:
- `.shazam/context/`
- `.shazam/tasks/`
- `.shazam/memories/`

Returns top-K most relevant chunks within the configured `context_budget` (default: 4,000 chars).

### Configuration

```yaml
config:
  context_history: 5      # last N tasks per agent
  team_activity: 10       # last N team tasks
  context_budget: 4000    # max chars injected into prompt
```

---

## 7. Plugin System

### Overview

Plugins are Elixir modules in `.shazam/plugins/*.ex`, compiled at runtime on `/start`. Execution order: alphabetical by filename (use `01_`, `02_` prefixes).

### Lifecycle Events

| Event | Phase | Can Mutate | Description |
|-------|-------|-----------|-------------|
| `on_init` | startup | -- | Called when `/start` boots agents |
| `before_task_create` | before | attrs or halt | Before a task is created |
| `after_task_create` | after | task | After a task is created |
| `before_task_complete` | before | result or halt | Before marking task complete |
| `after_task_complete` | after | result | After task is marked complete |
| `before_query` | before | prompt or halt | Before prompt is sent to agent |
| `after_query` | after | response | After agent responds |
| `on_tool_use` | notify | -- (observe only) | When an agent uses a tool |

### Writing a Plugin

```elixir
# .shazam/plugins/01_slack_notify.ex
defmodule ShazamPlugin.SlackNotify do
  use Shazam.Plugin

  @impl true
  def after_task_complete(task_id, result, ctx) do
    url = ctx.plugin_config["webhook_url"]
    if url do
      payload = Jason.encode!(%{text: "Task #{task_id} done in #{ctx.company_name}"})
      spawn(fn -> System.cmd("curl", ["-s", "-X", "POST", "-d", payload, url]) end)
    end
    {:ok, result}
  end
end
```

### Plugin Configuration (shazam.yaml)

```yaml
plugins:
  - name: slack_notify       # matches ShazamPlugin.SlackNotify (underscored)
    enabled: true
    events:                  # only trigger on these events (omit for all)
      - after_task_complete
      - after_task_create
    config:
      webhook_url: "https://hooks.slack.com/..."
  - name: auto_context
    enabled: false            # disabled without deleting the file
```

### Event Filtering

Use `events:` field to restrict when a plugin runs. Omit for all implemented callbacks.

### Commands

- `/plugins` -- list loaded plugins with event subscriptions
- `/plugins reload` -- hot-reload from disk (no restart needed)

---

## 8. Provider System

### Multi-CLI Support

Shazam supports multiple AI CLI providers. Set globally or per-agent:

```yaml
provider: claude_code           # default for all agents

agents:
  pm:
    provider: claude_code       # session-based
  senior_1:
    provider: codex             # stateless
  senior_2:
    provider: cursor            # stateless
  analyst:
    provider: gemini            # stateless
```

### Session vs Stateless

- **Session-based** (Claude Code): uses SessionPool for reuse across tasks. Context preserved, tokens saved.
- **Stateless** (Codex, Cursor, Gemini): ephemeral execution per task. Full context injected into prompt each time. Context persistence handled by ContextManager.

### Codex Fallback

If Claude hits rate limits, Shazam can fall back to Codex:
```bash
export CODEX_FALLBACK_MODEL="gpt-5-codex"
export CODEX_CLI_BIN="codex"
```

---

## 9. Configuration Reference

### shazam.yaml Complete Reference

```yaml
# Default AI CLI provider for all agents
provider: claude_code  # claude_code | codex | cursor | gemini

# Company definition
company:
  name: "MyTeam"
  mission: "Build and maintain the core product"
  workspace: "/path/to/project"  # Optional, defaults to CWD

# Domain access restrictions (optional)
domains:
  backend:
    description: "Backend services and API"
    paths:
      - "lib/"
      - "src/"
  frontend:
    description: "Frontend application"
    paths:
      - "app/"
      - "components/"

# Multi-repo workspaces (optional)
workspaces:
  backend:
    path: /home/user/projects/backend-api
    domains: ["lib/", "src/"]
  frontend:
    path: /home/user/projects/frontend-web
    domains: ["src/", "components/"]

# Tech stack (optional, auto-detected by shazam init)
tech_stack:
  language: "Elixir"
  framework: "Phoenix"
  database: "PostgreSQL"
  # or nested per workspace:
  dashboard:
    framework: "Vue.js"
    database: "Supabase"
  vscode:
    framework: "VS Code Extension API"

# Agent definitions
agents:
  pm:
    role: "Project Manager"
    # supervisor: null (top of hierarchy)
    budget: 200000                          # Token budget (omit for unlimited)
    model: "claude-haiku-4-5-20251001"      # AI model
    tools:                                  # Allowed tools
      - "Read"
      - "Grep"
      - "WebSearch"
    system_prompt: "You are a PM..."        # Custom prompt (optional)
    domain: "backend"                       # Restrict to domain paths
    provider: "claude_code"                 # AI CLI provider (overrides global)
    workspace: "backend"                    # Multi-repo workspace name
    config: ".shazam/agents/pm.md"          # Custom config file path
    heartbeat_interval: 60000               # Health check interval (ms)

  senior_dev:
    role: "Senior Developer"
    supervisor: "pm"                        # Reports to PM
    budget: 150000
    tools:
      - "Read"
      - "Edit"
      - "Write"
      - "Bash"
      - "Grep"
      - "Glob"
    domain: "backend"
    workspace: "backend"

  qa:
    role: "QA Engineer"
    supervisor: "pm"
    budget: 100000

# RalphLoop configuration
config:
  auto_approve: false       # true = subtasks execute immediately
  auto_retry: true          # Retry failed tasks automatically
  max_concurrent: 4         # Max parallel agent executions
  max_retries: 2            # Retry attempts before giving up
  poll_interval: 5000       # Task polling interval (ms)
  module_lock: true         # Prevent concurrent edits to same file
  peer_reassign: true       # Assign to idle peers if agent is busy
  qa_auto: true             # Auto-generate QA docs on task completion
  context_history: 5        # Last N tasks per agent for context
  team_activity: 10         # Last N team tasks for context
  context_budget: 4000      # Max chars injected into prompt

# Plugins (optional)
plugins:
  - name: slack_notifier
    enabled: true
    events: [after_task_complete, after_task_create]
    config:
      webhook_url: "https://hooks.slack.com/services/..."
  - name: auto_context
    enabled: true
```

### Default Values

| Setting | Default |
|---------|---------|
| Budget | 100,000 tokens |
| Heartbeat interval | 60,000 ms |
| Poll interval | 5,000 ms |
| Max concurrent | 4 |
| Max retries | 2 |
| HTTP port | 4040 |
| Context history | 5 tasks |
| Team activity | 10 tasks |
| Context budget | 4,000 chars |
| Session idle timeout | 15 min |
| Session task limit | 8 tasks |
| Circuit breaker threshold | 3 failures |
| Memory warning | 500 MB |

### Config File Loading Priority

For agent prompts: `config:` field in YAML -> `.shazam/agents/<name>.md` -> hardcoded preset

Config file locations: `./shazam.yaml` or `./.shazam/shazam.yaml`

---

## 10. TUI

### Architecture

The TUI is a separate Rust binary (`shazam-tui/`) that communicates with the Elixir backend via Erlang Port. The binary uses ratatui + crossterm for terminal rendering.

### Protocol

- Transport: Erlang Port with `:nouse_stdio` -- Rust uses stdin/stdout for terminal, fd 3/4 for JSON protocol with Elixir
- Format: Newline-delimited JSON
- Elixir -> Rust: render commands (events, status, overlays)
- Rust -> Elixir: user input events (commands, keystrokes)

### Overlays

| Overlay | Trigger | Content |
|---------|---------|---------|
| Tasks | `/tasks` | Task list with status, agent, title. Enter for detail view. |
| Agents | `/agents` | Agent list by domain with status, budget, tokens |
| Config | `/config` | Current configuration display |
| Dashboard | `/dashboard` | Live agent progress with sparklines |

### Status Bar

Format: `{company} | {state} | {sparklines} | P:{pending} R:{running} D:{done} | ${cost} | {branch} {status} | {memory}MB`

Components:
- Company name and running state
- Per-agent sparklines (activity heartbeat)
- Task counts by status
- Total cost in real-time
- Git branch with clean/dirty indicator
- BEAM + subprocess memory usage

### Markdown Rendering

Task results rendered with formatting:
- Headers (#, ##, ###) with color hierarchy
- Bullet lists with bullet icon
- Checkboxes with check/circle icons
- Code blocks, blockquotes, tables, horizontal rules
- Numbered lists with colored numbering

---

## 11. API

### REST Endpoints (port 4040)

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/api/companies` | List all running companies |
| `POST` | `/api/companies` | Create company from JSON body |
| `GET` | `/api/companies/:name/agents` | List agents |
| `PUT` | `/api/companies/:name/agents` | Update agent configs |
| `GET` | `/api/companies/:name/org-chart` | Org chart |
| `POST` | `/api/companies/:name/tasks` | Create task |
| `GET` | `/api/tasks` | List tasks with filters |
| `GET` | `/api/events/recent` | Recent events |

### WebSocket

Connect: `ws://localhost:4040/ws`

Events streamed:
- `agent_text_delta` -- streaming text from agent
- `agent_text_complete` -- final agent output
- `tool_use` -- agent used a tool
- `task_status_change` -- task state transition
- `system` -- system-level events
- `circuit_breaker_tripped` -- circuit breaker activated
- `circuit_breaker_reset` -- circuit breaker reset

---

## 12. File Structure

### Project Directory (`.shazam/`)

```
.shazam/
  shazam.yaml              # (or at project root)
  tasks/
    task_1.md              # YAML frontmatter + description + result
    task_2.md
  agents/
    pm.md                  # Editable agent config (frontmatter + system prompt)
    senior_1.md
    qa.md
  context/
    agents/
      senior_1/
        index.md           # Auto-generated links
        _learnings.md      # Extracted learnings (deduped)
        implement_jwt.md   # Topic file
      pm/
        index.md
        _learnings.md
    team_activity.md       # Chronological log (auto-trimmed)
  memories/
    SKILL.md               # Root skill index
    project/
      overview.md
      architecture.md
    agents/
      pm.md
    rules/
      testing.md
    decisions/
      001-auth-strategy.md
  memory/
    {agent_name}.md        # Legacy MemoryBank files
  plugins/
    01_slack_notify.ex     # Runtime-compiled plugins
    02_auto_context.ex
  plans/
    plan_1.md              # Execution plans
  qa/
    task_1_qa.md           # QA checklists
  attachments/
    image_001.png          # Pasted images
  logs/
    events.json            # If JSON logger plugin active
```

### Global Directory (`~/.shazam/`)

```
~/.shazam/
  tui_history              # Command history across sessions
  workspace.json           # Last workspace path
  company_{name}.json      # Company configs
  tasks_{company}.json     # Task boards
  *.json                   # Other Store keys
```

---

## 13. Workspaces

### Multi-Repo Setup

```yaml
workspaces:
  backend:
    path: /home/user/projects/backend-api
    domains: ["lib/", "src/"]
  frontend:
    path: /home/user/projects/frontend-web
    domains: ["src/", "components/"]
  mobile:
    path: /home/user/projects/mobile-app
    domains: ["lib/", "src/"]
```

### Workspace Enforcement

- Agents with `workspace:` field execute in their respective repo directory
- The `cwd` passed to the AI provider is set to the workspace path
- Domain restrictions apply relative to the workspace path
- Task counter is isolated per workspace (starts from task_1)
- Plans include workspace/repository info in PM prompts

### Commands

- `/workspaces` -- list configured workspaces with path validation

---

## 14. Resilience

### Circuit Breaker

- Trips after 3 consecutive task failures
- Auto-pauses all running RalphLoops via Registry
- Broadcasts warning event via EventBus
- Logs to FileLogger
- Manual reset: `/resume` or `CircuitBreaker.reset/0`
- `/health` shows circuit breaker status

### Health Check (`/health`)

Shows:
- Running agents and their status
- Stall detection (no events >30s)
- Circuit breaker state
- BEAM + subprocess memory usage
- Disk usage

### Graceful Degradation

- Warns when memory >500MB (configurable via `memory_warn_mb`)
- Warns when disk >95%

### Task Deduplication

Prevents creating duplicate tasks with same title + agent + company when one is already in pending or in_progress.

### Auto-Retry

- Failed tasks automatically retried with exponential backoff (5s, 15s, 30s)
- Max retries configurable (default: 2)
- Disabled with `auto_retry: false`

### Session Recovery

- Sessions auto-recycled after 8 tasks or 15 min idle
- Config changes (model, tools, cwd) force session recreation
- Periodic cleanup of idle sessions every 5 minutes

### Clean Shutdown

- `Application.stop(:shazam)` called before `System.halt(0)`
- Proper OTP cleanup of supervision tree
- EXIT signals handled gracefully

---

## 15. Memory/Context Flow

### Complete Flow Diagram

```
Task Completed
     |
     v
ContextManager.capture(agent, task, output, touched_files)
     |
     +---> Extract learnings (regex patterns)
     |       |
     |       +---> Deduplicate (Jaccard similarity > 0.7)
     |       |
     |       +---> Append to .shazam/context/agents/{name}/_learnings.md
     |
     +---> Route to topic file (keyword overlap matching)
     |       |
     |       +---> Existing topic file (if similarity >= 2) -> append
     |       |
     |       +---> New topic file (if no match) -> create
     |
     +---> Update index.md (auto-generated links)
     |
     +---> Append to team_activity.md (auto-trim at 200 entries)

Next Task Starts
     |
     v
TaskExecutor.run_agent_task(agent, task, company)
     |
     +---> ContextManager.build_context(agent, task)
     |       |
     |       +---> Load learnings ("What You Know")
     |       +---> Load recent work (last N topic entries)
     |       +---> Load team activity (last N entries)
     |       +---> ContextRAG.search(task.title) -> TF-IDF relevant context
     |       +---> Budget trim to context_budget chars
     |
     +---> GitContext.build_context(workspace)
     |       |
     |       +---> Current branch
     |       +---> Modified files
     |       +---> Recent commits
     |
     +---> SkillMemory.build_prompt(agent)
     |       |
     |       +---> Load from .shazam/memories/
     |
     +---> AgentQuery.build_instruction(agent, all_agents)
     |       |
     |       +---> Team Knowledge instruction (if other agents exist)
     |
     +---> Assemble final prompt
            |
            +---> system_prompt + skills + modules + memory + pm/impl rules
                  + domain restriction + tech stack + context + git
                  + agent query instructions
```

### Storage Layers

| Layer | Location | Purpose | Persistence |
|-------|----------|---------|-------------|
| ContextManager | `.shazam/context/` | Cross-task learning, topic files | Filesystem (markdown) |
| ContextRAG | In-memory (built on demand) | TF-IDF search over all .md files | Ephemeral |
| SkillMemory | `.shazam/memories/` | Structured skill-graph | Filesystem (markdown) |
| MemoryBank | `.shazam/memory/` | Legacy per-agent memory | Filesystem (markdown) |
| SessionPool | In-memory | Claude session reuse | Ephemeral (recreated on start) |
| Store | `~/.shazam/*.json` | Companies, tasks, workspace | JSON files |
| TaskFiles | `.shazam/tasks/` | Human-readable task records | Filesystem (markdown) |
| AgentConfig | `.shazam/agents/` | Editable agent prompts | Filesystem (markdown) |

---

## Appendix A: CLI Commands Quick Reference

### External Commands

```
shazam init                    # Create shazam.yaml
shazam                         # Open interactive shell (auto)
shazam shell                   # Open interactive shell (explicit)
shazam start                   # Boot server (HTTP API mode)
shazam status                  # Show running companies
shazam stop [--all]            # Stop companies
shazam task "title" [--to agent]
shazam org                     # Display org chart
shazam logs [agent]            # Stream events
shazam agent add <name>        # Add agent
shazam apply [-f file]         # Apply YAML changes
shazam dashboard               # TUI dashboard
shazam version                 # Show version
shazam update                  # Auto-update to latest tag
```

### Interactive Shell Commands

```
/start                         # Boot agents and RalphLoop
/stop                          # Stop agents
/pause                         # Pause RalphLoop
/resume                        # Resume RalphLoop
/dashboard                     # Agent progress dashboard
/status                        # Company overview
/agents                        # List agents
/org                           # Org chart
/tasks [--clear|--sync|--export]
/task <title> [--to agent]     # Create task
/approve [id] [--all]          # Approve task
/aa                            # Approve all (shortcut)
/reject <id>                   # Reject task
/msg <agent> <message>         # Message agent
/auto-approve [on|off]         # Toggle auto-approve
/config                        # Show config
/agent add|edit|remove <name>  # Agent management
/team create <domain>          # Create team from template
/pause-task <id>
/resume-task <id>
/kill-task <id>
/retry-task <id>
/retry-all                     # Retry all failed
/delete-task <id>
/search <query>                # Search tasks
/export [file]                 # Export to markdown
/review <pr>                   # Review PR
/review --learn|--post|--check|--resolve|--patterns
/workspaces                    # List workspaces
/plugins [reload]              # Plugin management
/plan <description>            # Create execution plan
/plan --list|--show|--approve
/qa [--generate|--validate|--report|--auto]
/memory-bank [--update]
/health                        # System health
/clear                         # Clear screen
/help                          # Show help
/quit                          # Exit
```

---

## Appendix B: Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `CODEX_FALLBACK_MODEL` | `gpt-5-codex` | Fallback model for rate limits |
| `CODEX_CLI_BIN` | `codex` | Path to Codex CLI binary |

---

## Appendix C: Project Source Layout

```
lib/
  shazam.ex                          # Public API
  shazam/
    application.ex                   # OTP supervision tree
    company.ex                       # Company GenServer
    company/
      builder.ex                     # Agent config building
    ralph_loop.ex                    # Per-company task execution loop
    task_board.ex                    # ETS-backed task CRUD
    task_board/
      persistence.ex                 # ETS <-> disk serialization
    task_executor.ex                 # Prompt building & execution
    task_executor/
      prompt_builder.ex              # All prompt-building functions
    task_scheduler.ex                # Task selection algorithm
    subtask_parser.ex                # Extract subtasks from output
    task_templates.ex                # Pre-built prompt templates
    task_files.ex                    # Markdown task persistence
    retry_policy.ex                  # Exponential backoff
    orchestrator.ex                  # Agent execution engine
    session_pool.ex                  # Session reuse
    agent_worker.ex                  # Agent config struct
    agent_presets.ex                 # Role templates
    agent_inbox.ex                   # Per-agent message queue
    agent_config.ex                  # Editable agent configs
    agent_pulse.ex                   # Activity sparkline
    agent_query.ex                   # Agent-to-agent queries
    hierarchy.ex                     # Org chart + cycle detection
    module_manager.ex                # File-level access control
    context_manager.ex               # Cross-provider context
    context_rag.ex                   # TF-IDF retrieval
    git_context.ex                   # Git-awareness
    project_detector.ex              # Tech stack detection
    circuit_breaker.ex               # Auto-pause on failures
    plugin.ex                        # Plugin behaviour
    plugin_manager.ex                # Plugin loading + pipeline
    plugin_loader.ex                 # Runtime .ex compilation
    provider.ex                      # Provider behaviour
    provider/
      claude_code.ex                 # Claude Code provider
      codex.ex                       # Codex provider
      cursor.ex                      # Cursor provider
      gemini.ex                      # Gemini provider
      resolver.ex                    # Name -> module mapping
    store.ex                         # JSON persistence
    metrics.ex                       # Token tracking
    file_logger.ex                   # File logging
    memory_bank.ex                   # Legacy memory
    skill_memory.ex                  # Skill-graph memory
    pr_reviewer.ex                   # PR review agent
    plan_manager.ex                  # Execution planning
    qa_manager.ex                    # QA checklists
    api/
      router.ex                      # REST API router
      event_bus.ex                   # Event pub/sub
      websocket.ex                   # WebSocket handler
      helpers.ex                     # API utilities
      routes/
        company_routes.ex
        task_routes.ex
        ralph_routes.ex
        skill_routes.ex
        workspace_routes.ex
        misc_routes.ex
    cli.ex                           # CLI entry point
    cli/
      repl.ex                        # Interactive shell
      yaml_parser.ex                 # YAML parsing
      formatter.ex                   # Terminal formatting
      helpers.ex                     # CLI utilities
      shared.ex                      # Shared CLI functions
      http_client.ex                 # HTTP client for API
      tui_port.ex                    # Rust TUI communication
      tui_port/
        commands.ex                  # Command router
        commands/
          system.ex                  # /start, /stop, /help, etc.
          tasks.ex                   # /task, /approve, /reject, etc.
          agents.ex                  # /agent, /team, /org, etc.
          review.ex                  # /review with sub-flags
          tools.ex                   # /plan, /qa, /memory-bank, /plugins
        helpers.ex                   # TUI helper functions
        status.ex                    # Status bar data
      commands/
        init.ex                      # shazam init
        start.ex                     # shazam start
        stop.ex                      # shazam stop
        status.ex                    # shazam status
        task.ex                      # shazam task
        org.ex                       # shazam org
        logs.ex                      # shazam logs
        dashboard.ex                 # shazam dashboard
        apply.ex                     # shazam apply
        agent_add.ex                 # shazam agent add
config/
  config.exs                         # Application configuration
test/
  ...                                # 371+ tests
shazam-tui/
  src/                               # Rust TUI source
  Cargo.toml
```
