# TDD Philosophy Reference (Short)

"Design happens during REFACTORING, not during coding."

Implications for TOML mirror docs:
- Prefer stable intent & interface over implementation detail
- Keep descriptions high-signal (WHAT + WHY)
- Refactor-driven updates: when exports/contracts change, TOML must change
- Avoid copying code into docs; mirror the architectural meaning

Use TOML as an evolving design map:
- public interfaces (props, function params/returns)
- responsibilities and constraints
- architecture role and dependency boundaries
