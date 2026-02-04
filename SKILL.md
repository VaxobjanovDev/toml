---
name: toml
description: "Generates structured TOML documentation that mirrors an entire codebase's architecture for AI agent consumption. Optimized to reduce token usage in future sessions by relying on generated TOML (toml/**) as the primary knowledge source. Supports JavaScript/TypeScript and React (JSX/TSX), including shared libraries and API-client code."
---

# TOML Documentation Generator Skill

**Skill Name:** toml  
**Version:** 1.1.0  
**Author:** Custom Skill  
**Category:** Documentation, Code Analysis, AI Tools

---

## Description

Generates structured TOML documentation that mirrors a repository’s architecture for AI agent consumption. Use when the user asks to:

- "document my project for AI"
- "generate TOML files"
- "create metadata for my components"
- "index my codebase"
- "map out my architecture"
- "prepare documentation for an AI agent"

**Primary goal:** create a durable `toml/` knowledge base so future requests can be answered using TOML instead of re-reading source files (token-saving).

---

## No Runtime Requirements

This skill does **not** require Python or any extra runtime.  
It is instruction-based: the agent reads source files (when needed) and writes TOML outputs.

---

## Overview

Transform source code into structured, AI-readable TOML documentation. This skill creates a `toml/` directory that mirrors your project structure with semantic metadata files designed for AI agent comprehension.

---

## Default Scope (IMPORTANT)

- Default to scanning the **repository root (`.`)**, not only `src/`.
- If `src/` exists, prioritize it **but do not ignore** other top-level folders like:
    - `shared/`, `lib/`, `libs/`, `packages/`, `apps/`, `server/`, `api/`, `services/`, `utils/`, `scripts/`
- **Never** skip a folder solely because it is named `api`.

---

## Token Efficiency Rules (MUST FOLLOW)

1) On follow-up requests, read `toml/INDEX.md` (and/or `toml/INDEX.toml`) and relevant `toml/**/*.toml` first.
2) Only open source files if TOML is missing, clearly outdated, or the user explicitly requests code inspection.
3) If updating, regenerate TOML **only for affected files**, not the whole repo.
4) Prefer short, high-signal summaries in TOML to reduce future token usage.

---

## Core Workflow

### Step 1: Analyze Project Structure
- Scan repository root (`.`) unless the user specifies a subdirectory
- Build a file tree of candidate source files
- Identify file categories:
    - **components** (React components)
    - **pages/routes** (app/page routing)
    - **hooks** (use*)
    - **utilities** (pure helpers)
    - **types** (types/interfaces)
    - **api/service clients** (fetch/axios clients, request builders)
    - **state/store** (Redux/Zustand/etc.)
    - **configs** (only include if they contain runtime logic)

### Step 2: Process Each File
For each relevant file, extract:
- **Intent**: what problem it solves (WHAT + WHY)
- **Interface**: props/params/returns/events
- **Dependencies**: internal + external
- **Usage Context**: where it sits in architecture and constraints

### Step 3: Generate TOML Files (Mirror Hierarchy)
- Output folder: `toml/`
- For each source file `path/to/File.ext`, write:
    - `toml/path/to/File.toml` (replace extension with `.toml`)
- Preserve the exact folder structure.

Example mapping:
```
src/components/auth/LoginForm.jsx
  → toml/src/components/auth/LoginForm.toml

shared/api/httpClient.js
  → toml/shared/api/httpClient.toml
```

---

## JS / JSX / TS / TSX Classification Rules (IMPORTANT)

### React Components
Classify as `type="component"` if file (usually `.jsx` or `.tsx`) exports a React component, e.g.:
- `export default function ComponentName() { return (...) }`
- `export function ComponentName() { return (...) }`
- `const ComponentName = (...) => (...)`
- `React.FC<...>` patterns

### Pages / Routes
Classify as `type="page"` when component represents a route/page:
- file lives under `/pages/`, `/app/`, `/routes/`
- or uses router conventions of your framework

### Hooks
Classify as `type="hook"` when:
- filename starts with `use` (e.g., `useUser.ts`)
- returns an object/tuple and uses React hooks inside

### API Clients / Services (MUST INCLUDE, especially under shared/api)
Classify as `type="api_client"` or `type="service"` when file:
- uses `fetch`, `axios`, `ky`, `graphql-request`, `Apollo`, etc.
- builds request/response shapes
- exports functions like `getUser`, `createOrder`, `fetchSomething`

### Utilities
Classify as `type="utility"` for:
- pure functions, formatters, validators, mappers

### Types
Classify as `type="types"` for:
- `.d.ts`
- `types.ts`, `*.types.ts`
- files exporting interfaces/types

---

## TOML Structure Requirements (MANDATORY)

Every TOML file MUST include these tables:

### 1) `[metadata]`
```toml
[metadata]
name = "EntityName"
type = "component|page|hook|utility|types|service|api_client|store|config"
path = "relative/path/to/file"
description = "1-2 sentences describing functional intent (WHAT + WHY)."
```

Optional (recommended for update detection):
```toml
[metadata.source]
generated_at = "YYYY-MM-DD"
# source_hash = "optional"
```

### 2) `[properties]` (interface contract)
**Components**: document props  
**Utilities**: document parameters  
**API clients/services**: document public functions and inputs
```toml
[properties]
paramOrProp = { type = "string|number|boolean|object|function|enum", required = true, default = "value", description = "Impact on behavior" }
```

Hooks MUST include return values:
```toml
[properties.return]
data = { type = "unknown", description = "Returned data" }
loading = { type = "boolean", description = "Request in-flight status" }
```

Utilities should include return meaning too:
```toml
[properties.return]
result = { type = "unknown", description = "Return value meaning" }
```

Multiple exports:
```toml
[properties.exports]
SomeExport = { type = "function|object|type|component", description = "What it is and why it exists" }
```

### 3) `[context]` (architectural positioning)
```toml
[context]
state_management = "How state is managed (local, context, store, caching, etc.)"
dependencies = ["InternalModuleOrComponent", "externalLibrary"]
usage_guideline = "Where/how to use; required preconditions"
architectural_role = "presentation|routing|business_logic|data_access|integration|infrastructure"
```

Optional but recommended fields (use underscore names):
- `performance_notes`
- `error_handling`
- `security_notes` (especially for api clients)
- `caching_behavior`
- `testing_notes`

---

## File Processing Rules

### Include
- `.ts`, `.tsx`, `.js`, `.jsx` files containing logic
- `shared/**` including **`shared/api/**`**
- API client/service files (fetch/axios/graphql)
- stores/state files (redux/zustand/mobx)
- route/page definitions with logic

### Exclude (minimal)
- `node_modules/`, `.git/`
- build outputs: `dist/`, `build/`, `.next/`, `out/`, `coverage/`
- assets: `.png`, `.jpg`, `.svg`, `.webp`, `.ico`
- styles only: `.css`, `.scss`, `.module.css`
- tests: `.test.*`, `.spec.*`, `__tests__/`

### Special Cases
- `index.ts` barrel exports: exclude unless they contain real logic
- configs: include only if they contain runtime logic used by app; otherwise exclude

---

## Quality Standards

- Avoid generic descriptions
- Explain WHY it exists and where it belongs architecturally
- Do NOT include source code in TOML
- Preserve folder structure exactly
- Do NOT skip `[context]`

---

## Required Outputs

### 1) `toml/INDEX.md`
- List all documented files with:
    - type
    - 1-line summary
    - link/path to TOML file

### 2) `toml/INDEX.toml` (token saver)
Machine-friendly manifest mapping source → TOML file:

```toml
[repo]
root = "."
generated_at = "YYYY-MM-DD"

[[files]]
path = "shared/api/httpClient.js"
toml = "toml/shared/api/httpClient.toml"
type = "api_client"
summary = "HTTP client wrapper for authenticated requests with retry/error normalization."
```

---

## Execution Strategy

When user triggers this skill:

1) Ask: "Scan entire repo (`.`) or a specific directory?" Default: **entire repo**.
2) Build file tree; show top-level folders detected
3) Process files in order:
    - utilities/services/api clients → stores → hooks → components → pages/routes
4) Create mirrored `toml/` structure while processing
5) Report progress every ~20 files (or per folder)
6) Generate `toml/INDEX.md` + `toml/INDEX.toml`

For large projects:
- Process in batches
- Allow user to include/exclude subdirectories (but default is entire repo)

---

## Anti-Patterns to Avoid

1) Never include source code in TOML files
2) Never flatten hierarchy — preserve exact folder structure
3) Never generate trivial descriptions
4) Never document deep implementation details — focus on intent + interface + context
5) Never skip the `[context]` table

---

## Success Criteria

A well-documented project enables an AI agent to:
- understand architecture without re-reading source
- locate relevant files via `toml/INDEX.*`
- update only impacted areas
- reduce token usage by relying primarily on TOML docs

The `toml/` directory becomes the repo’s "DNA map" optimized for AI comprehension.
