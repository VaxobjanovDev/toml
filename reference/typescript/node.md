# TypeScript + Node Server Add-on Rules

Detect Node server:
- express/fastify/koa/hapi/nest OR server patterns

Classification:
- controllers/handlers: `service`
- DTOs/types: `types`
- repositories/adapters: `service` (data_access)
- outbound clients: `api_client`

Extraction:
- prefer exported type signatures
- summarize DTOs instead of expanding every field if too large

Context:
- include `security_notes` for auth/token/session
- include `error_handling` for global error strategy
