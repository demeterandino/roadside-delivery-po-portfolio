# Rollback strategy (field-deployed roadside devices)

This document outlines a practical rollback approach for roadside deployments.
Rollback is a core safety mechanism: it minimizes downtime and limits impact when a release causes instability.

---

## 1) Objectives
- Restore service quickly when a new version causes issues
- Reduce MTTR by enabling remote recovery (avoid field visits where possible)
- Ensure rollback is predictable, auditable, and tested

---

## 2) What “rollback” can mean (levels)

### Level 1 — Feature/config rollback (fastest)
- Disable a feature flag
- Revert configuration to last known good version
- Apply a safe “degraded mode” profile

**Use when:** the defect is in behavior controlled by config/flags and can be mitigated without changing binaries.

### Level 2 — Application rollback
- Revert the edge application to the previous stable version

**Use when:** the defect is in app logic, performance, memory usage, or stability.

### Level 3 — Base image / OS rollback
- Revert to the previous base image or system package set

**Use when:** issues stem from OS libraries, drivers, runtime, or low-level compatibility.

### Level 4 — Firmware rollback (rare and cautious)
- Revert firmware (camera/modem/RSU) only with strict safeguards

**Use when:** firmware upgrade introduced instability and rollback path is validated and safe.

> Principle: prefer the smallest rollback that restores service.

---

## 3) Recommended deployment design to enable rollback

### A) Dual-slot (A/B) or previous-version retention
- Keep **current stable** and **new candidate** on device (A/B slots), or
- Retain at least **one previous known-good** package/image locally

### B) State reporting
Devices must report:
- current active version (app/base image/agent)
- last deployment state and timestamp
- last successful health check time
- variant identifiers (hardware rev / firmware versions)

### C) Configuration snapshots
- Store last known good config version and a rollback config snapshot
- Validate config schema compatibility before activation/rollback

### D) Integrity and security
- package signature/checksum validation
- role-based access control for rollback triggers
- audit logs for rollback actions

---

## 4) Rollback triggers (when to roll back)

Rollback should be initiated when:
- a Sev1 incident is attributed to the release
- cohort KPIs breach agreed thresholds for a sustained period
- stability issues affect critical devices/sites
- deployment failure rate exceeds threshold in canary/pilot

Typical signals:
- heartbeat loss / offline spikes
- event lag and dropped events increasing
- CPU/memory spikes after activation
- ingestion errors increasing (4xx/5xx) or duplicate rate anomalies

---

## 5) Rollback decision workflow (Go/No-Go aligned)

1) **Detect & confirm** the issue (monitoring + logs)
2) **Assess blast radius** (which cohort/variants are impacted)
3) **Decide rollback level** (config/feature → app → image → firmware)
4) **Execute rollback** on affected cohort
5) **Validate recovery** (health checks + KPIs)
6) **Freeze rollout** until RCA and mitigation plan are agreed
7) **Create follow-up backlog items** (prevention, tests, monitoring, matrix updates)

---

## 6) Rollback execution checklist (practical)

- [ ] Confirm affected cohort and variants (hardware/firmware/app)
- [ ] Confirm rollback target version is available and approved
- [ ] Ensure rollback action is authorized (RBAC) and logged
- [ ] Execute rollback in a controlled order (small batch first if needed)
- [ ] Verify device returns to healthy state within X minutes (target MTTR)
- [ ] Validate business flow recovered (events arriving, error rates normal)
- [ ] Record outcome, evidence, and timeline
- [ ] Update release notes / stakeholder comms

---

## 7) Post-rollback actions (learning loop)

### Root cause analysis (RCA)
- Identify the failure mode (variant-specific? environment-specific?)
- Confirm whether detection could have been earlier (alert tuning)
- Confirm whether rollback was smooth (process/tooling gaps)

### Convert learnings into backlog
Common improvement stories:
- stronger pre-checks (variant matrix enforcement)
- better stop criteria and automatic rollout pause
- expanded Ring 0 scenarios (network drop, reconnect burst, long-run soak tests)
- better diagnostics and structured logs
- updated SAT/UAT checklists for the impacted conditions

---

## 8) PO responsibilities (what I would drive)
A Product Owner typically:
- ensures rollback is treated as a deliverable (not an afterthought)
- defines acceptance criteria for “rollback readiness”
- aligns stakeholders on stop criteria and rollback thresholds
- balances feature scope with reliability work needed for safe deployment
- ensures documentation and communication are included in Definition of Done
