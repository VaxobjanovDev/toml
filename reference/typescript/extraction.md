# TypeScript Extraction Rules (Interface-first, Stable)

> Design happens during REFACTORING, not during coding.
> See: references/tdd.md

## General
- Never include source code
- Focus on public surface area: exports, props, params, returns
- Avoid deep internal details

## Extract
1) Exported symbols:
    - default export
    - named exports
2) For exported functions:
    - param names + high-level types
    - return meaning + type (if simple)
3) For components:
    - props interface/type name if present
    - important props only (high-impact)
4) For hooks:
    - return contract in `[properties.return]`
5) Dependencies:
    - external packages
    - internal modules

## Type handling rules
- If union/complex generic → summarize as `unknown` + description
- Prefer simple type strings: `string|number|boolean|object|enum|function|unknown`
- Use description to capture meaning:
    - "User id string"
    - "Options for pagination"
    - "API response envelope"

## Stores
- record store shape at high level
- record key actions/selectors exports

## API clients/services
- list endpoint methods in `[properties.exports]`
- include auth/caching/error handling notes in `[context]`
