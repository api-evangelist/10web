---
name: 10web-generate-ai-website
description: Generate a full AI-powered WordPress website with 10Web, from business description to a live hosted site.
api: 10Web API V1
generated: '2026-07-17'
method: generated
source: openapi/10web-openapi.yaml
auth: x-api-key header
operations:
- POST /v1/ai/generate_sitemap
- POST /v1/ai/generate_site
- POST /v1/hosting/ai-website
- GET /v1/account/websites
- POST /v1/account/websites/{website_id}/convert-to-live
---

# Generate an AI website with 10Web

Every request is sent to `https://api.10web.io` with an `x-api-key` header.

1. **Generate the outline** — `POST /v1/ai/generate_sitemap` with the business
   description. Returns an outline, styles, and secondary pages you can review.
2. **Generate the site** — `POST /v1/ai/generate_site` (or
   `POST /v1/ai/generate_site_from_sitemap` to reuse the outline from step 1),
   or `POST /v1/hosting/ai-website` to create a hosting website with AI content
   in one call. Capture the returned `website_id`.
3. **Confirm creation** — subscribe to the `website_generation_completed` and
   `wvc_action_completed` webhooks, or poll `GET /v1/account/websites` and match
   the `website_id`.
4. **Promote a demo to live** — if the site was created as a demo, call
   `POST /v1/account/websites/{website_id}/convert-to-live`.

## Rules
- Auth: `x-api-key` (Reseller Dashboard key). Missing/invalid -> 401
  `{"status":"error","message":"Unauthorized access"}`.
- Errors use `{status, message}` (not RFC 9457). Retry 429/500 with backoff.
- Prefer webhooks over polling; dedupe redelivered events by `event_id`.
