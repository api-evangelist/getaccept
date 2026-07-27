---
name: Send a document for e-signature
description: Create a GetAccept document (from a template or an uploaded file), add recipients, send it for signature, then track status and remind signers.
api: openapi/getaccept-openapi-original.json
operations:
  - Authenticate
  - ListTemplates
  - UploadDocument
  - CreateDocument
  - SendDocument
  - ListRecipients
  - GetDocumentStatus
  - SendReminder
---

# Send a document for e-signature

Use the GetAccept API v1 (`https://api.getaccept.com/v1`) to design, send, and track a document for electronic signature.

## Auth
- Authenticate with OAuth2 (authorization code) or a bearer token. Obtain/verify a session with `Authenticate` (`POST /auth`); confirm with `TestAuthentication` (`GET /test`).
- Send the token as `Authorization: Bearer <token>`.

## Steps
1. **Pick a source.** Either list reusable templates with `ListTemplates` (`GET /templates`) and inspect fields/roles via `ListFields` and `ListRoles`, or upload a file with `UploadDocument` (`POST /upload`).
2. **Create the document.** Call `CreateDocument` (`POST /documents`) with the template id or uploaded file plus recipients. This single operation can both create and send ("Create and send document or template").
3. **Send (if created as draft).** If the document was created without sending, call `SendDocument` (`POST /documents/{documentId}/send`).
4. **Confirm recipients.** Verify signers with `ListRecipients` (`GET /documents/{documentId}/recipients`).
5. **Track status.** Poll `GetDocumentStatus` (`GET /documents/{documentId}/status`) or, better, subscribe to webhook events (see the subscribe-to-document-events skill) rather than polling.
6. **Nudge.** Send `SendReminder` (`POST /documents/{documentId}/reminders`) to pending signers.

## Conventions
- Errors return a plain JSON envelope `{ error, description, status }` — read `status` for the HTTP code and `error`/`description` for the reason (see errors/getaccept-problem-types.yml).
- List endpoints accept `limit` and `offset` for pagination.
- No idempotency-key mechanism is documented; guard against duplicate sends in your own workflow.
