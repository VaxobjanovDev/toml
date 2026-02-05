# TypeScript Detection Rules

TS signals:
- `tsconfig.json` exists OR
- `typescript` appears in dependencies/devDependencies OR
- non-test `.ts/.tsx` files exist

If TS detected, prefer TS rules even if `.js/.jsx` also exists.

React signals:
- `.tsx` exists OR `react` dependency exists

> See: references/routing.md
