# Change management (roadside releases)

This document describes a practical change management approach for roadside delivery.
The goal is to introduce changes safely to field-deployed systems with clear ownership, approvals,
maintenance windows, and stakeholder communication.

---

## 1) Why change management matters in roadside delivery
Roadside systems are deployed in real environments with:
- limited field access windows,
- higher cost of failures (outages, data loss, truck rolls),
- mixed variants and mixed versions during rollout.

Change management reduces operational risk and improves predictability.

---

## 2) Change types (and how strict we are)

### Standard change (low risk)
Examples:
- dashboard/report updates
- non-breaking configuration changes with validation
- minor fixes with proven rollback

**Process:** pre-approved path, lighter review, still audited.

### Normal change (moderate risk)
Examples:
- edge app updates
- deployment tool changes
- new telemetry metrics or alert rules

**Process:** standard approvals, staged rollout required, rollback tested.

### Emergency change (high urgency)
Examples:
- critical security patches
- widespread outages requiring immediate mitigation

**Process:** expedited approvals + retrospective review post-incident.

---

## 3) Roles & responsibilities (typical model)
- **Product Owner:** scope, priorities, acceptance criteria, stakeholder alignment
- **Engineering/Architecture:** technical solution, compatibility, risk assessment
- **QA/Test:** test evidence and readiness recommendation
- **Operations/Release Manager:** rollout execution, monitoring, rollback actions
- **Field Engineering:** site constraints, commissioning readiness, SAT evidence
- **Customer/Authority (if applicable):** UAT acceptance and operational sign-off
- **Program/Project leadership:** final Go/No-Go for major rollouts

---

## 4) Change lifecycle (from request to rollout)

### Step 1 — Change request & classification
Capture:
- what changes and why (problem/value)
- impacted sites/variants
- risk level (standard/normal/emergency)
- required maintenance window

### Step 2 — Impact assessment
Assess:
- compatibility (variant matrix)
- operational impact (downtime risk, monitoring needs)
- security/compliance impact (certs, access, retention)
- dependencies (backend, vendors, field access)

### Step 3 — Plan & approvals
- define rollout cohorts (canary/pilot/wider rollout)
- define stop criteria and rollback plan
- confirm acceptance gates for this release
- obtain approvals based on risk level

### Step 4 — Execute rollout
- run Ring 0 validation
- canary activation with hypercare monitoring
- pilot activation after canary exit criteria
- wider rollout in phased cohorts

### Step 5 — Post-change review
- confirm KPI outcomes
- document incidents/near-misses
- update runbooks, variant matrix constraints, and backlog actions

---

## 5) Required artifacts for a “normal change” (minimum)
- Release scope summary (what is in / out)
- Risk assessment (top risks + mitigations, RAID updates)
- Supported variants list (from the matrix)
- Deployment plan (cohorts + schedule + maintenance windows)
- Stop criteria (pause rules) and rollback plan
- Test evidence summary (Ring 0 / FAT / SAT / Pilot UAT as relevant)
- Monitoring plan (KPIs and dashboards to watch in hypercare)
- Communication plan (who is informed, when, and how)

---

## 6) Maintenance windows (practical considerations)
- Align activation with agreed windows to minimize service disruption
- Prefer “download first, activate later” approach
- Confirm field constraints (lane closures, site access rules)
- Keep a clear on-call plan during activation windows

---

## 7) Communication plan (stakeholder-friendly)
For each change/release, communicate:
- objective and impact (what changes, why it matters)
- rollout scope (which cohorts/sites)
- timeline and maintenance windows
- risks and mitigations
- rollback approach and responsibilities
- where to monitor status (dashboards) and escalation path

---

## 8) Auditability (what must be traceable)
Ensure the system can answer:
- who initiated the change (deployment/rollback/config)
- when it happened
- what versions/configs were applied
- which sites/devices were affected
- what the outcome was (success/failure/rollback)

This is especially important for regulated environments and customer trust.

---

## 9) PO responsibilities (what I would drive)
A Product Owner typically:
- maintains clear scope boundaries and prioritization
- ensures acceptance criteria cover operability and risk controls
- aligns stakeholders on go/no-go criteria and trade-offs
- ensures release notes and communication are complete
- converts post-change learnings into backlog improvements
