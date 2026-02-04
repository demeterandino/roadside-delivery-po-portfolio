# KPIs & SLOs (Roadside Delivery)

This document defines a simple KPI set for a roadside delivery product and illustrates how SLO-style targets can be used
to drive Go/No-Go decisions during canary/pilot and to guide continuous improvement during operations.

> Note: Thresholds below are illustrative for portfolio purposes. Real targets are agreed per customer, site type, and regulatory context.

---

## 1) Principles for choosing KPIs
Good roadside KPIs should:
- reflect both **service continuity** and **data correctness**
- be measurable from telemetry/logs (not subjective)
- support clear decisions (pause rollout / rollback / proceed)
- be stable enough to track trends (before/after comparisons)

---

## 2) Core KPIs (definitions)

### A) Availability (Uptime)
**Definition:** percentage of time a site/device is operational (healthy and delivering expected signals/events).  
**Why it matters:** outages directly impact service, data completeness, and SLA commitments.

**Example SLO (illustrative):**
- Canary/Pilot cohort availability ≥ 99.5% over the observation window

---

### B) Event lag (end-to-end delay)
**Definition:** time from event occurrence at roadside to successful availability in the central backend for processing.  
**Why it matters:** high lag can indicate connectivity or processing bottlenecks and may affect downstream matching/processing.

**Example SLO (illustrative):**
- Median < 2s; p95 < 10s during pilot

---

### C) Dropped events (loss rate)
**Definition:** percentage of events that are expected but never arrive to the backend (not just delayed).  
**Why it matters:** lost events can lead to incorrect outcomes and loss of trust.

**Example SLO (illustrative):**
- < 0.1% dropped events; alert on spikes above threshold

---

### D) Ingestion success/error rates (API quality)
**Definition:** backend ingestion outcomes over time:
- success rate (2xx)
- client errors (4xx)
- server errors (5xx)
- rate limiting (429)

**Why it matters:** shows integration health and helps distinguish device-side vs backend-side issues.

**Example SLO (illustrative):**
- 5xx < 0.5% during pilot; sustained spikes trigger rollout pause

---

### E) Duplicate rate (idempotency hits)
**Definition:** proportion of events received that are duplicates (same event_id).  
**Why it matters:** duplicates are normal with retries; the key is that duplicates do not create double processing.

**Example SLO (illustrative):**
- Duplicate rate tracked and explained; sudden spikes indicate connectivity/retry behavior issues

---

### F) Rollback rate
**Definition:** share of devices/sites in a cohort that required rollback after activation.  
**Why it matters:** a high rollback rate suggests release instability or variant incompatibilities.

**Example SLO (illustrative):**
- < 5% rollback rate in canary/pilot cohorts

---

### G) Incident metrics (stability signal)
**Definition:** incident count and severity trend attributable to a release or a component.  
**Why it matters:** incidents are the real-world “quality signal” for field systems.

**Example SLO (illustrative):**
- No Sev1 attributable to the release during the pilot observation window

---

### H) MTTR (Mean Time To Restore/Repair)
**Definition:** average time to restore service after an outage/incident.  
**Why it matters:** affects SLA performance and operational cost.

**Example SLO (illustrative):**
- MTTR for cohort incidents trending downward release-over-release

---

## 3) Optional domain KPIs (if applicable)

### Read-rate / accuracy (e.g., ANPR/classification)
**Read-rate:** percentage of passings where an identifier/classification is successfully produced.  
**Accuracy:** percentage of produced results that are correct (validated sample).

**Example SLO (illustrative):**
- Targets agreed per environment (day/night, weather), measured consistently and audited.

---

## 4) How KPIs are used in delivery decisions

### Canary / Pilot Go/No-Go (example)
Proceed to pilot (from canary) only if:
- availability within threshold
- event lag within threshold
- dropped events within threshold
- no Sev1 incidents attributable to release
- rollback not required (or rollback cause resolved)

Proceed to wider rollout (from pilot) only if:
- KPIs remain stable across representative sites and conditions
- variant-specific issues are understood and constrained (variant matrix updated)
- hypercare exit criteria are met

---

## 5) Measurement notes (what data must exist)
To measure these KPIs, ensure:
- devices emit heartbeat/telemetry (online status, version tags)
- ingestion pipeline logs event_id and outcomes (success/duplicate/errors)
- metrics are tagged by device_id, site_id, version, and variant identifiers
- dashboards support cohort views (canary vs pilot vs production)

---

## 6) Backlog examples derived from KPI gaps
If KPI shows deterioration, typical backlog responses include:
- improve buffering and reconnect behavior (reduce dropped events)
- strengthen idempotency handling (reduce duplicate impact)
- add/adjust alerts (faster detection, lower MTTR)
- enforce variant compatibility rules (reduce rollback rate)
- refine deployment pause rules (limit blast radius)
