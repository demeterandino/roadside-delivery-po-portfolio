# US-005 — Business event ingestion API with idempotency and retry-safe behavior

## Summary
As a Business Backend, I want to ingest roadside events via a stable API contract with idempotency, so that events can be
processed reliably even when devices retry due to intermittent connectivity.

## Background / Context
Roadside connectivity may be unstable (LTE/fiber interruptions). Devices may retry sending events if acknowledgements are not
received. Without idempotency, retries can create duplicate records (duplicate transactions / duplicate processing).

This story defines a minimal ingestion contract and behaviors needed for reliable operations.

## Scope
### In scope
- API endpoint(s) and payload schema (minimum required fields)
- Idempotency behavior using `event_id`
- Error handling and response codes
- Retry guidance for devices
- Basic observability requirements for ingestion

### Out of scope
- Full business rules processing (handled downstream)
- Long-term analytics design
- UI design

## Proposed API contract (simplified)

### Endpoint
- `POST /events`

### Required headers
- `Content-Type: application/json`
- `Idempotency-Key: <event_id>` (optional if `event_id` is in payload; at least one must be present)

### Minimum payload fields (example)
- `event_id` (string, globally unique per event)
- `device_id` (string)
- `site_id` (string)
- `timestamp_utc` (ISO 8601)
- `event_type` (string)
- `payload` (object; event-specific fields)
- `schema_version` (string, e.g., "1.0")

> Note: exact field naming is illustrative. The key is having stable identifiers, timestamps, and versioning.

## Acceptance Criteria

### A) Validation and responses
1. For a valid new event, the API returns **201 Created** (or 202 Accepted if asynchronous ingestion is used).
2. If required fields are missing or invalid, the API returns **400 Bad Request** with an error body that includes:
   - machine-readable error code
   - human-readable message
   - list of invalid/missing fields
3. If authentication/authorization fails (if applicable), return **401/403**.

### B) Idempotency (duplicate-safe ingestion)
4. The backend must treat `event_id` as the unique key for ingestion.
5. If the same `event_id` is submitted multiple times:
   - only one record is created/processed
   - the API responds consistently (choose one behavior and document it):
     - **200 OK** with body indicating "already processed", OR
     - **409 Conflict** with a clear "duplicate event_id" error code
6. Idempotency must hold across common failure scenarios:
   - device retries after timeout
   - device retries after a transient 5xx
   - device retries after reconnecting from offline

### C) Retry-safe behavior (device guidance)
7. For transient backend failures, API returns **5xx** (e.g., 503) and devices should retry with exponential backoff.
8. The backend must not partially process an event in a way that breaks idempotency (e.g., no duplicates on retry).
9. If rate limiting is applied, API returns **429 Too Many Requests** and includes a retry guidance (e.g., `Retry-After`).

### D) Versioning and backward compatibility
10. The API supports schema evolution:
   - adding new optional fields does not break existing processing
   - `schema_version` is stored and visible for diagnostics
11. Breaking schema changes require a versioned endpoint or contract update strategy (documented).

### E) Observability (minimum)
12. Each ingested request is traceable via correlation IDs:
   - `event_id` is logged and searchable
   - request/response outcome is logged (success/duplicate/validation_error/server_error)
13. Metrics exist for:
   - ingestion success rate
   - duplicate rate (idempotency hits)
   - error rates by type (4xx vs 5xx)
   - latency distribution (p50/p95)

## Non-functional Requirements
- **Reliability:** no duplicate processing from retries; graceful under intermittent networks
- **Performance:** ingestion handles expected burst patterns (e.g., reconnect upload bursts)
- **Security:** secure transport (TLS), authentication/authorization if required
- **Operability:** logs/metrics allow incident triage without field access

## Test Approach
### Ring 0 (Lab/Staging)
- Submit a valid event → verify 201/202
- Submit invalid payload → verify 400 with field-level errors
- Submit same event twice → verify idempotency behavior (200/409) and only one stored record
- Simulate timeouts and retries → confirm no duplicates
- Simulate reconnect burst (batch send) → confirm stability and latency behavior

### Pilot validation
- Monitor duplicate rate and error rates during canary/pilot window
- Confirm device retry behavior does not cause duplicate records

## Dependencies
- Central authentication (if used)
- Logging/metrics platform to surface ingestion KPIs
- Agreement on event schema with upstream (roadside) producers

## Definition of Done
- Contract documented and reviewed with stakeholders
- Idempotency implemented and verified by tests
- Observability (logs + metrics) in place
- Retry guidance documented for device-side implementation
