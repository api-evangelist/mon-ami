---
name: Intake and enroll a client
description: Create a new client in Mon Ami, record intake documents, and enroll them in a program.
api: openapi/mon-ami-openapi.yml
operations: [createClient, createClientDocument, listPrograms, listProgramEnrollments, getClient]
---

# Intake and enroll a client (Mon Ami)

Use the Mon Ami REST API to intake a new aging/disability-services client and enroll
them in a program.

## Auth
HTTP Basic. Send `Authorization: Basic base64(uid:secret)` on every request.
Base URL: `https://app.monami.io/api`. All bodies and responses are JSON.

## Steps
1. **Create the client** — `POST /clients` (`createClient`). Capture the returned
   `id` for subsequent calls.
2. **Attach an intake document** — `POST /clients/{client_id}/documents`
   (`createClientDocument`). Documents support templates and InterRAI iCodes for
   assessment data.
3. **Find the target program** — `GET /programs` (`listPrograms`), paginate with
   `page` / `per_page` (max 50) and filter with `q[field]=value` if needed.
4. **Confirm enrollment state** — `GET /clients/{client_id}/program_enrollments`
   (`listProgramEnrollments`) to verify the client's current enrollments.
5. **Verify** — `GET /clients/{client_id}` (`getClient`) to confirm the record.

## Rules
- Pagination: responses carry `links` and `meta`; never assume more than 50 per page.
- Errors use `{ "errors": [ { "message": "..." } ] }`. Treat `422` as validation
  failure (read the messages), `401` as bad credentials, `429` as rate-limited (back off).
- No idempotency-key mechanism is documented — avoid blind retries on `POST`.
