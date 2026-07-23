---
name: 10web-staging-and-backup
description: Safely change a 10Web website using staging environments and backups, then push to live.
api: 10Web API V1
generated: '2026-07-17'
method: generated
source: openapi/10web-openapi.yaml
auth: x-api-key header
operations:
- POST /v1/hosting/websites/{website_id}/backup/run
- GET /v1/hosting/websites/{website_id}/backup/list
- POST /v1/hosting/websites/{website_id}/staging/enable
- POST /v1/hosting/websites/{website_id}/push-to-staging
- POST /v1/hosting/websites/{staging_website_id}/push-to-live
- POST /v1/hosting/websites/{website_id}/backup/{backup_id}/restore
---

# Stage, back up, and push changes to live

Every request is sent to `https://api.10web.io` with an `x-api-key` header.

1. **Take a safety backup** — `POST /v1/hosting/websites/{website_id}/backup/run`,
   then confirm via `GET /v1/hosting/websites/{website_id}/backup/list` or the
   `backup_created` webhook.
2. **Enable staging** — `POST /v1/hosting/websites/{website_id}/staging/enable`.
   Capture the returned `staging_website_id` (also delivered in the
   `staging_environment_enabled` webhook).
3. **Sync content to staging** — `POST /v1/hosting/websites/{website_id}/push-to-staging`,
   then make and test changes on the staging site.
4. **Promote to live** —
   `POST /v1/hosting/websites/{staging_website_id}/push-to-live`. The
   `environment_synced` webhook reports `sync_type: to_live`.
5. **Roll back if needed** —
   `POST /v1/hosting/websites/{website_id}/backup/{backup_id}/restore` using a
   `backup_id` from step 1.

## Rules
- Auth: `x-api-key`. Errors use `{status, message}`; retry 429/500 with backoff.
- Distinguish `website_id` (live) from `staging_website_id` (staging) — several
  operations key off the staging id.
- Dedupe redelivered webhooks by `event_id`.
