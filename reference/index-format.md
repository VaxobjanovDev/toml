# TOML Schema + Index Format (Core Contract)

> Design happens during REFACTORING, not during coding.
> See: references/tdd.md

## Per-file TOML schema (MANDATORY)

Every TOML file must contain:

### 1) [metadata]
```toml
[metadata]
name = "EntityName"
type = "component|page|hook|utility|types|service|api_client|store|config"
path = "relative/path/to/file"
description = "1-2 sentences describing WHAT + WHY."
Optional:

[metadata.source]
generated_at = "YYYY-MM-DD"
source_hash = "optional"

2) [properties] (interface contract)

Components:

[properties]
propName = { type = "string|number|boolean|object|function|enum|unknown", required = true, default = "optional", description = "Impact on behavior" }


Hooks MUST include returns:

[properties.return]
data = { type = "unknown", description = "Returned data meaning" }
loading = { type = "boolean", description = "Request in-flight status" }
error = { type = "unknown", description = "Error when request fails" }


Utilities SHOULD include returns:

[properties.return]
result = { type = "unknown", description = "Return value meaning" }


Multiple exports:

[properties.exports]
SomeExport = { type = "function|object|type|component|unknown", description = "What it is and why it exists" }


API clients/services (public functions):

[properties.exports]
getUser = { type = "function", description = "Fetches user by id; used by profile flows" }

3) [context] (architectural positioning)
[context]
state_management = "local|context|store|server-cache|unknown + 1 line explanation"
dependencies = ["InternalModule", "external-lib"]
usage_guideline = "Where/how to use; required preconditions"
architectural_role = "presentation|routing|business_logic|data_access|integration|infrastructure"


Optional but recommended:

performance_notes

error_handling

security_notes

caching_behavior

testing_notes

toml/INDEX.md (human)

Must list every documented file:

type

one-line summary

link/path to TOML file

Example line format:

api_client — HTTP client wrapper for authenticated requests — toml/shared/api/httpClient.toml

toml/INDEX.toml (machine, token-saver)
[repo]
root = "."
generated_at = "YYYY-MM-DD"

[[files]]
path = "shared/api/httpClient.js"
toml = "toml/shared/api/httpClient.toml"
type = "api_client"
summary = "HTTP client wrapper for authenticated requests with retry/error normalization."


Rules:

Always keep path = source path, toml = mirrored toml path

Remove entry if source file deleted

Update entry if type/summary changes

Design happens during REFACTORING, not during coding.
See: references/tdd.md

---

## `references/diff-update.md`

```md
# Incremental Update Rules (Diff / Change Handling)

> Design happens during REFACTORING, not during coding.
> See: references/tdd.md

## Goal

When code changes, update ONLY the impacted TOML mirrors and indexes.

## Supported change types

### 1) Modified file
Do:
- Re-read only that source file (if needed)
- Regenerate `toml/<same path>.toml`
- Update `toml/INDEX.md` + `toml/INDEX.toml` entry summary/type if changed

### 2) Added file
Do:
- Classify file
- Generate new mirrored TOML
- Add new entry to both indexes

### 3) Deleted file
Do:
- Remove corresponding TOML mirror file
- Remove entries from both indexes

### 4) Renamed / moved file
Do:
- Move TOML mirror path to match new source path
- Update indexes:
  - old `path` removed
  - new `path` added (or updated)
- If content also changed, regenerate TOML content after move

## Outdated detection

Treat TOML as outdated if ANY:
- TOML missing
- `metadata.source.generated_at` is older than last known change date (if user provides it)
- `metadata.source.source_hash` mismatch (if you implement hashing)
- Classification changed (e.g., utility → service)

## Minimal safe update (when limited info)

If user only provides:
- changed file list (no diff)
Then:
- regenerate TOML for those files only
- do not touch others

If user provides:
- diff/patch for file
Then:
- update TOML focusing on exported interface changes:
  - new exports
  - removed exports
  - changed prop/param contract
  - changed dependencies (imports)

## Index sync checklist

After any update, ensure:
- every `toml/**/*.toml` has a corresponding `[[files]]` entry
- every `[[files]]` entry points to an existing TOML file
- no orphan TOML files exist after deletions/moves

> Design happens during REFACTORING, not during coding.
> See: references/tdd.md

