# Acceptance gates (FAT / SAT / UAT) and Go/No-Go criteria

This document describes a practical acceptance approach for a roadside product where hardware, field conditions,
and operations readiness matter as much as feature delivery.

The goal is to make releases **safe to deploy**, **operable**, and **measurably stable** before wider rollout.

---

## 1) Why acceptance gates exist in roadside delivery

Roadside systems are deployed in the field and often run unattended.
A defect can result in:
- service outages (availability impact),
- data loss (dropped events),
- operational costs (truck rolls),
- compliance/security risk (data handling, access control),
- delayed project milestones.

Acceptance gates reduce risk by validating readiness in stages.

---

## 2) Gate overview (from lab to rollout)

**Gate 0 — Lab/Staging (Ring 0)**
- Purpose: verify functionality and integration in a controlled environment.
- Outcome: “technically ready to test on real hardware / pilot sites”.

**Gate 1 — FAT (Factory Acceptance Test)**
- Purpose: validate the solution against the specification before field rollout.
- Outcome: “approved for site installation / commissioning”.

**Gate 2 — SAT (Site Acceptance Test)**
- Purpose: validate performance and correctness in the real physical site.
- Outcome: “approved for pilot / controlled operations”.

**Gate 3 — Pilot UAT (Customer Acceptance)**
- Purpose: validate user/customer acceptance and operational readiness under real traffic conditions.
- Outcome: “approved to rollout”.

**Gate 4 — Wider rollout & Hypercare exit**
- Purpose: monitor stability during rollout and confirm sustained KPIs.
- Outcome: “release is stable and transitions to normal operations”.

---

## 3) Gate 0 — Lab/Staging (Ring 0) acceptance

### Entry criteria
- Story-level Definition of Done met for included scope
- Unit and integration tests green
- Interface/API contract reviewed (schema + versioning)
- Release notes drafted (what changes, risks, rollback plan)

### Validation checklist (examples)
- Basic device lifecycle actions work: configure → run → report telemetry
- Event ingestion works with retry behavior (simulated network failures)
- Store-and-forward behavior tested (disconnect/reconnect + reboot)
- Idempotency tested (duplicate event submission does not create duplicates)
- Observability present: logs, metrics, and alert hooks

### Exit criteria
- No critical defects open
- Known issues documented + mitigations agreed
- Candidate approved for FAT package

---

## 4) Gate 1 — FAT (Factory Acceptance Test)

### What is FAT?
A structured test (often with customer involvement) to confirm the solution meets requirements before going to the field.

### FAT focus areas
- Functional coverage against requirements
- Basic performance under controlled conditions
- Compatibility with target hardware/firmware baseline
- Security basics (auth, access control where applicable)

### Typical outputs
- FAT test report (pass/fail + evidence)
- Baseline versions documented (hardware + firmware + software)
- Open issues list with severity and plan
- Go/No-Go recommendation for site deployment

---

## 5) Gate 2 — SAT (Site Acceptance Test)

### What is SAT?
On-site verification after installation and commissioning, confirming the system works in the real environment.

### SAT focus areas
- Connectivity stability (LTE/fiber quality, VPN/TLS stability)
- Correct site configuration (e.g., lane/site mapping where applicable)
- Device health under real power/temperature conditions
- End-to-end flow: edge → central platforms (device management + backend)

### Typical outputs
- SAT checklist signed-off
- Site configuration version locked
- Monitoring/alerts confirmed active
- Handover to Operations (runbook + escalation path)

---

## 6) Gate 3 — Pilot UAT (Customer acceptance in pilot scope)

### Purpose
Validate that the delivered capability is acceptable to the customer in real usage,
and that operations can run it reliably.

### Pilot scope principles
- Start small (canary) → expand to a representative pilot set
- Use clear Go/No-Go KPIs (below)
- Run a defined “hypercare” window after activation

### Typical outputs
- Customer sign-off (where applicable)
- KPI report for pilot window
- Known issues + roadmap decisions (fix now vs later)

---

## 7) Gate 4 — Scale rollout & Hypercare exit

### Hypercare definition
A planned period after go-live/rollout steps where monitoring is intensified and response times are shorter.
This is where “release stability” is proven.

### Hypercare exit criteria (example)
- KPIs stable for X days across pilot/rolled-out sites
- No Sev1 incidents attributable to the release
- Rollback rate below threshold and reasons documented
- Operational runbooks updated and validated

---

## 8) Go/No-Go KPI examples (roadside-appropriate)

Use a small set of KPIs that reflect both reliability and data correctness.

**Availability (uptime)**
- Target: e.g., ≥ 99.5% per site during pilot window (example threshold)

**Event lag**
- Target: e.g., median < 2s, 95th percentile < 10s (example thresholds)

**Dropped events**
- Target: e.g., < 0.1% (example threshold), with alerts on spikes

**Rollback rate**
- Target: e.g., < 5% of sites in rollout cohort (example threshold)

**Incident trends**
- Target: no increase in Sev1/Sev2 compared to baseline for X days

**(Optional, if applicable) Read-rate / accuracy**
- Targets agreed per environment (day/night/weather), measured consistently

> Note: thresholds are illustrative. Real thresholds are defined per customer/project and site environment.

---

## 9) Decision roles (who approves what)

A typical model:
- Engineering/QA: recommends technical readiness (Gate 0/1)
- Field Engineering: confirms site readiness (Gate 2)
- Operations: confirms operability and monitoring readiness (Gate 2/3/4)
- Customer/Authority: confirms UAT acceptance (Gate 3)
- Project/Program leadership: final Go/No-Go decision for scaling (Gate 4)

---

## 10) How this maps to backlog items (PO view)

To make gates real, include backlog items for:
- telemetry/metrics and alert rules,
- idempotency and deduplication,
- store-and-forward and reconnect behavior,
- deployment safety (download/verify/activate),
- rollback mechanisms and documentation,
- test evidence and acceptance artifacts.

This ensures “operability and acceptance” are treated as deliverables, not afterthoughts.
