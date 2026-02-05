# JavaScript + Next.js Add-on Rules

Detect Next.js:
- `next` dependency OR `pages/` or `app/` directory conventions

Classification tweaks:
- `pages/**` files are typically `page`
- `app/**/page.(js|jsx)` is `page`
- `app/**/layout.(js|jsx)` is `component` (layout role = routing/presentation hybrid)
- `app/api/**` route handlers should be `service` or `api_client` depending on role

Context fields (recommended):
- `architectural_role = "routing"` for Next route entry files
- `caching_behavior` if uses Next caching primitives (best-effort)

> See: references/index-format.md
