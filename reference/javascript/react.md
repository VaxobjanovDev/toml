# JavaScript + React Rules (Classification)

> Design happens during REFACTORING, not during coding.
> See: references/tdd.md

## Classification

### component
- exports React component (default or named)
- returns JSX
- common patterns:
    - `export default function X() { return (...) }`
    - `export function X() { return (...) }`
    - `const X = () => (...)`

### page
- lives under `/pages`, `/app`, `/routes`
- or framework routing conventions apply (see nextjs.md if Next detected)

### hook
- filename starts with `use`
- uses hooks internally (`useState`, `useEffect`, etc.)
- returns tuple/object used by components

### api_client / service
- uses `fetch/axios/ky/graphql-request/Apollo`
- exports request functions
- organizes endpoint calls or domain service logic

### utility
- pure helpers: formatting, mapping, validation, parsing
- no rendering, no request orchestration (otherwise service)

### store
- Redux/Zustand/MobX patterns, or `store/` folder
- exports selectors/actions/store hooks

## Properties extraction (JS style)

Because JS lacks static types:
- infer prop/param names from usage patterns, defaults, docs, or destructuring
- if unclear: type = "unknown"
- always document:
    - required vs optional (best-effort)
    - defaults if present

## Context

Infer:
- architectural_role (presentation/routing/data_access/etc.)
- dependencies (imports)
- usage_guideline (where used, expected env)

> See: references/javascript/extraction.md
