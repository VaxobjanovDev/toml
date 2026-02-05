# Routing (Detect → Ruleset)

This file defines how the core skill selects the correct ruleset.

> Design happens during REFACTORING, not during coding.
> See: references/tdd.md

## Detection order (recommended)

1) Identify language: JS vs TS
2) Identify framework: React vs Next.js vs Node server
3) Resolve final ruleset stack

## Minimal signals (do not overthink)

### Language
- TypeScript if `tsconfig.json` exists OR `"typescript"` appears in dependencies/devDependencies OR any `.ts/.tsx` exists in non-test folders
- Otherwise JavaScript

### Framework
- React if `react` dependency exists OR `.jsx/.tsx` exists OR JSX syntax present
- Next.js if `next` dependency exists OR `/app` or `/pages` uses Next patterns
- Node server if server frameworks exist (`express|fastify|koa|hapi|nest`) OR server entry patterns detected (`server.ts`, `app.ts`, `index.ts` in server folder)

## Routing table (core)

### Primary ruleset
- TS + React → `references/typescript/react.md` + `references/typescript/extraction.md`
- JS + React → `references/javascript/react.md` + `references/javascript/extraction.md`
- TS (no React) → `references/typescript/extraction.md`
- JS (no React) → `references/javascript/extraction.md`

### Add-on rules (optional)
- Next.js:
    - If TS: `references/typescript/nextjs.md`
    - If JS: `references/javascript/nextjs.md`
- Node server:
    - If TS: `references/typescript/node.md`
    - If JS: `references/javascript/node.md`

## Output expectation

After routing, the agent must be able to answer:
- "Which classifier rules apply?"
- "How do we extract interface contracts?"
- "How do we treat routes/pages?"

> Design happens during REFACTORING, not during coding.
> See: references/tdd.md
