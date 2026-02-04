# Release Notes — R1.0 (Roadside Delivery baseline)

**Release ID:** R1.0  
**Type:** Baseline release (pilot-ready)  
**Target:** Canary → Pilot → Wider rollout (phased)  
**Owner:** Product Owner (scope), Ops/Release Manager (execution)

> Note: This is a fictional example release note for portfolio purposes.

---

## 1) Purpose of this release
This release establishes a stable baseline for operating roadside devices with:
- safe staged rollout (canary → pilot → wider rollout),
- reliable business event ingestion with idempotency,
- minimum observability for monitoring and incident response.

---

## 2) Included scope (high-level)

### Delivered
- **Staged rollout workflow** with stop criteria and rollback  
  - Reference: [03-backlog/stories/US-003-canary-rollout.md](../03-backlog/stories/US-003-canary-rollout.md)
- **Business event ingestion API** with idempotency and retry-safe behavior  
  - Reference: [03-backlog/stories/US-005-api-event-ingestion-idempotency.md](../03-backlog/stories/US-005-api-event-ingestion-idempotency.md)
- **Acceptance gates & go/no-go KPI model**  
  - Reference: [05-quality/acceptance-gates.md](../05-quality/acceptance-gates.md)
- **Variant matrix governance (baseline definition)**  
  - Reference: [07-variants/variant-matrix.md](../07-variants/variant-matrix.md)

### Not included (explicitly deferred)
- Advanced dashboard UX (assumed existing platform capabilities)
- Major hardware baseline expansion beyond the pilot variant set
- Long-term analytics/data platform integrations

---

## 3) Deployment plan (phased)

### Ring 0 (Lab/Staging)
- Validate deployment workflow (download → verify → activate)
- Validate API ingestion contract and idempotency scenarios
- Validate pause rules and rollback actions

### Canary
- **Scope:** 1 low-risk site/device  
- **Duration:** 48 hours observation window  
- **Goal:** verify stability and monitoring signals before pilot expansion

### Pilot
- **Scope:** 5–10 representative sites/devices  
- **Duration:** 7–14 days  
- **Goal:** validate reliability under different conditions and confirm operational readiness

### Wider rollout
- **Scope:** phased cohorts by region/corridor  
- Triggered only after pilot exit criteria are met

---

## 4) Rollback strategy (summary)
Rollback is supported for canary and pilot cohorts:
- revert to previous stable package/image
- restore last known good configuration (if applicable)
- confirm device returns to healthy state or is flagged for investigation

Rollback actions are audited (who/when/why).

---

## 5) Monitoring & KPIs during hypercare
Hypercare is active for each cohort activation window.

### KPIs to monitor (illustrative thresholds)
- **Availability (uptime):** cohort ≥ 99.5%
- **Event lag:** median < 2s; p95 < 10s
- **Dropped events:** < 0.1% with spike alerts
- **Rollback rate:** < 5% within cohort
- **Incidents:** no Sev1 attributable to release during observation window

### Operational dashboards (minimum)
- device heartbeat/online status
- deployment state per device
- ingestion success/error rates (4xx/5xx) and duplicate rate (idempotency hits)
- alerts for queue depth (if buffering is used) and connectivity instability

---

## 6) Compatibility / supported variants
This release is approved for the defined pilot baseline variants.
See [07-variants/variant-matrix.md](../07-variants/variant-matrix.md) for:
- supported combinations (hardware rev, firmware, base image, app version)
- constraints and rollout rules
- known incompatible combinations (blocked targets)

---

## 7) Known issues / limitations (example)
- If connectivity is unstable, devices may upload events in bursts after reconnect; monitor backend latency and 429/5xx rates.
- Some variants may require SAT tuning for night conditions before being added to pilot cohorts (documented in the variant matrix).

---

## 8) Go/No-Go criteria (pilot exit)
Proceed from pilot to wider rollout only if:
- KPIs remain within agreed thresholds for the full pilot observation window
- no Sev1 incidents attributable to this release
- rollback rate is within threshold and root causes are understood
- operational runbooks and escalation paths are confirmed

---

## 9) Artifacts & references
- Context: [01-context/context-diagram.md](../01-context/context-diagram.md)
- Acceptance gates: [05-quality/acceptance-gates.md](../05-quality/acceptance-gates.md)
- Variant matrix: [07-variants/variant-matrix.md](../07-variants/variant-matrix.md)
- US-003 rollout: [03-backlog/stories/US-003-canary-rollout.md](../03-backlog/stories/US-003-canary-rollout.md)
- US-005 API ingestion: [03-backlog/stories/US-005-api-event-ingestion-idempotency.md](../03-backlog/stories/US-005-api-event-ingestion-idempotency.md)
