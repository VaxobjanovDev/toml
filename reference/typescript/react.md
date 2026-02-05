# TypeScript + React Rules (Classification)

> Design happens during REFACTORING, not during coding.
> See: references/tdd.md

## Classification (same categories as JS)
- component
- page
- hook
- api_client / service
- utility
- types
- store
- config

## TS-specific extraction advantage
Prefer explicit types:
- props interfaces
- function param/return annotations
- exported types

But keep it high-level:
- do not expand deep generics
- capture meaning, not mechanics

## Types category
If file mainly exports types/interfaces:
- `type="types"`
- In `[properties.exports]`, list the key types/interfaces with short intent

> See: references/typescript/extraction.md
