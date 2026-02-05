# TypeScript + Next.js Add-on Rules

Detect Next.js:
- `next` dependency OR `/app` or `/pages`

Classification tweaks:
- `app/**/page.tsx` → `page`
- `app/**/layout.tsx` → `component` (role often routing/presentation)
- `app/api/**` handlers → `service` (server integration)
- `pages/**` route components → `page`

Context (recommended):
- `architectural_role = "routing"` for page entries
- `security_notes` for auth middleware/route protection
- `caching_behavior` if Next caching used (best-effort)
