---
name: Record service delivery and subscribe to updates
description: Look up a client's care plan and services, log a service record, and subscribe to webhooks for live updates.
api: openapi/mon-ami-openapi.yml
operations: [listClients, listCarePlans, listCarePlanServices, createServiceRecord, createWebhook, listWebhooks]
---

# Record service delivery and subscribe to updates (Mon Ami)

## Auth
HTTP Basic. `Authorization: Basic base64(uid:secret)`. Base URL `https://app.monami.io/api`.

## Steps
1. **Find the client** — `GET /clients` (`listClients`), filter with
   `q[field]=value`.
2. **Read the care plan** — `GET /clients/{client_id}/care_plans` (`listCarePlans`),
   then `GET /clients/{client_id}/care_plans/{care_plan_id}/services`
   (`listCarePlanServices`) to see planned services.
3. **Log the delivered service** — `POST /service_records` (`createServiceRecord`).
4. **Subscribe to live updates** — `POST /webhooks` (`createWebhook`) with your
   handler URL; confirm with `GET /webhooks` (`listWebhooks`). Implement an HTTP
   handler per the Mon Ami delivery-semantics docs.

## Rules
- Errors envelope: `{ "errors": [ { "message": "..." } ] }`. Handle `422` (validation),
  `409` (conflict), `429` (slow down).
- Pagination via `page` / `per_page` (max 50); use `links`/`meta` to walk pages.
- Webhooks give live notifications when platform events occur; remove a subscription
  with `DELETE /webhooks/{id}` (`deleteWebhook`).
