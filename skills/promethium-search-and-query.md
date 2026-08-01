---
name: promethium-search-and-query
description: Find a governed datamap in Promethium and run a federated SQL query against enterprise data via the Universal Query Engine (Trino).
api: Promethium API
base_url: https://api.prod.promethium.ai
operations:
- POST /auth/login/{tenant}
- POST /datamap/search
- GET /datamap/{id}
- POST /query
generated: '2026-07-20'
method: generated
source: https://docs.promethium.ai/docs/api-reference/
---

# Search and Query Enterprise Data

Use this skill to discover a governed data asset (datamap) and execute a query
against it through Promethium's federated Universal Query Engine.

## Steps

1. **Authenticate.** `POST /auth/login/{tenant}` with `Content-Type: application/json`
   and a body of `{ "email": ..., "password": ... }` (local service account) — or use
   the SSO browser flow. Keep the returned `id_token`.
2. **Send auth on every call.** Add the header `idtoken: <id_token>` to all subsequent
   requests (custom header, not `Authorization: Bearer`).
3. **Search for the datamap.** `POST /datamap/search` with a body containing `q`
   (query string), `from`, `size`, `sort_key`, `sort_value`. Read the top result's
   `id`, `datamap_name`, and `field_count`.
4. **Inspect the datamap.** `GET /datamap/{id}` to review `datamap_fields` before
   querying.
5. **Run the query.** `POST /query` on the Trino Stream host
   `https://{tenant}-trino-stream.{env}.promethium.ai/query` with a SQL body,
   authenticating via `idtoken`, HTTP Basic, or `Authorization: Bearer`.

## Rules

- No idempotency key is supported; do not assume retries are safe on writes.
- Refresh expired tokens with `POST /auth/refresh/{tenant}`; log out with
  `GET /auth/logout/{tenant}`.
- Respect role/domain authorization — results are scoped to the caller's granted domains.
