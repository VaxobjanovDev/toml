# JavaScript Extraction Rules (Interface-focused)

> Design happens during REFACTORING, not during coding.
> See: references/tdd.md

## General rules
- Never include source code in TOML
- Summarize WHAT + WHY (not HOW)
- Prefer exports/contracts over internal helpers

## Extract:
1) Exports list (default + named)
2) For each exported function/component:
    - inputs (props/params)
    - outputs (return meaning)
    - important side effects (network, storage, navigation)
3) Dependencies:
    - internal imports (relative paths)
    - external libs (package imports)

## Components
- props:
    - infer from function signature destructuring and usage
    - include defaults if present

## Hooks
- document return contract in `[properties.return]`
- include `loading/error` if hook is async/data-fetching

## API clients/services
- list exported public methods in `[properties.exports]`
- capture auth/caching/error handling in context

## If unsure
- use `type="unknown"`
- still fill `usage_guideline` and `architectural_role` best-effort
