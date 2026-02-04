# Roadmap (Roadside Delivery) — Now / Next / Later

This is a fictional, simplified roadmap to demonstrate how I plan delivery in a roadside environment.
The roadmap balances **operability**, **safe rollout**, and **domain capability** while respecting field constraints and variants.

---

## Guiding idea
In roadside systems, early roadmap value often comes from:
1) making the system **observable and operable**, and
2) making deployments **safe and reversible**,
before expanding to additional sites and variants.

---

## NOW (0–6 weeks) — Baseline operability and safe rollout
**Goal:** establish a stable baseline for canary/pilot readiness.

- Basic telemetry & dashboards (heartbeat, versions, health)
- Event ingestion contract with idempotency and retry-safe behavior
- Staged rollout workflow (download → verify → activate) with pause rules
- Rollback readiness (tested rollback path, audited actions)
- Acceptance gates defined (Ring 0 / FAT / SAT / Pilot UAT)
- Variant matrix baseline (approved combinations + blocked targets)

**Success signal:** canary and pilot run with stable KPIs and predictable recovery.

---

## NEXT (6–12 weeks) — Reduce operational cost and improve diagnostics
**Goal:** lower MTTR and reduce field visits through better detection and troubleshooting.

- Improved diagnostics collection (logs/traceability, correlation IDs)
- Alert tuning: fewer false positives, earlier detection
- Automated certificate rotation + expiry alerting
- Config validation and safe activation (prevent misconfiguration)
- Better handling of reconnect bursts (performance hardening)
- Expanded variant coverage with clear constraints (SAT evidence)

**Success signal:** MTTR decreases, repeat incidents drop, rollback rate decreases.

---

## LATER (3–6 months) — Repeatable rollout across regions and customer setups
**Goal:** make delivery repeatable across multiple deployments and environments.

- Deployment automation enhancements (cohort orchestration, reporting)
- Standardized commissioning playbooks and site acceptance evidence packs
- Lifecycle policy for hardware/firmware/software baselines (EOL/EOS handling)
- Increased coverage of supported variants and site profiles
- Continuous improvement loop from incidents and KPI trends into backlog

**Success signal:** predictable releases across regions with consistent acceptance outcomes.

---

## Notes
- This roadmap assumes parallel workstreams (engineering, QA, operations, field).
- Exact timing and scope depend on customer commitments, field access windows, and platform readiness.
