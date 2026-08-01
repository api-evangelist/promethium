---
name: promethium-catalog-data-asset
description: Register a new datamap from an SQL definition and enrich metadata (descriptions, tags, domains) across tables, views, and datamaps in Promethium.
api: Promethium API
base_url: https://api.prod.promethium.ai
operations:
- POST /auth/login/{tenant}
- POST /datamap/create
- POST /metadata/import
- POST /audits/logs/search
generated: '2026-07-20'
method: generated
source: https://docs.promethium.ai/docs/api-reference/
---

# Catalog and Govern a Data Asset

Use this skill to define a governed datamap and bulk-enrich its metadata so it is
discoverable and trusted.

## Steps

1. **Authenticate** and capture `id_token`; send `idtoken: <id_token>` on every call.
2. **Create the datamap.** `POST /datamap/create` with `Content-Type: application/json`,
   required fields `name` and `sql`, plus optional `description`, `tags`, `owner`,
   `sme`, `requestor`, and a `custom_attr` object (`product_type`, `product_line`,
   `datamap_type`, `version`, `priority`, `value`, ...).
3. **Enrich metadata in bulk.** `POST /metadata/import` with a `metadata_objects`
   array (max 50 per call). Each object sets `type` (TABLE|VIEW|DATAMAP), `id`,
   `display_name`, `description`, and add/remove `tags` and `domains` at object and
   field level. Read back `success_count` / `failure_count` and per-object
   `error_message`.
4. **Verify via audit.** `POST /audits/logs/search` (filter by `advance_options.user_name`
   and `start`/`end` epoch range) to confirm the create/import actions were recorded.

## Rules

- `metadata/import` is capped at 50 objects per request; page larger sets.
- No idempotency key is documented — de-duplicate before retrying a create/import.
- Metadata changes are governed by the caller's role and domain grants.
