# Incident → Backlog (Operations learnings into product improvements)

This document describes a practical approach for handling incidents in a roadside environment and converting recurring
operational problems into prioritized backlog items.

Roadside delivery is not only about shipping features—it’s about keeping field-deployed systems **reliable**, **observable**,
and **recoverable** with minimal field visits.

---

## 1) Definitions (plain language)

### Incident
An unplanned event that degrades service (e.g., device offline, data not arriving, error spikes).
Incidents are handled with urgency to restore service.

### Problem (recurring incident pattern)
A repeated incident pattern with the same root cause (or likely root cause).
Problems are handled by identifying root cause and preventing recurrence.

### Backlog item
A planned change (bug fix, improvement, automation, monitoring, or process change) that reduces future risk/cost or improves outcomes.

---

## 2) Goal: clear separation of "restore now" vs "prevent later"

**Restore now (Incident management):**
- stop user/business impact quickly
- triage, mitigate, recover
- capture evidence for investigation

**Prevent later (Product/Engineering backlog):**
- remove root cause
- improve detection and diagnosis
- improve rollout/rollback safety
- reduce MTTR and field visits (truck rolls)

A useful rule:
- One-off incidents may stay in ops tickets.
- Repeating incidents become backlog items once a pattern is confirmed or risk is high.

---

## 3) Typical incident categories in roadside systems

### Connectivity / Network
- LTE instability, VPN drops, high packet loss
- reconnection loops causing CPU spikes
- prolonged offline buffering leading to backlog spikes on reconnect

### Device health / resources
- storage filling up (logs, images, queue)
- memory leaks, CPU throttling, overheating
- failing peripherals (camera stream drop)

### Data pipeline / integration
- ingestion API errors (4xx/5xx spikes)
- duplicate events due to retries without idempotency
- schema mismatch or contract change breaking ingestion

### Deployment / configuration
- failed activation, partial updates
- wrong site/lane configuration (mis-mapping)
- incompatible variant deployed (firmware/app mismatch)

### Security / certificates
- certificate expiry → devices unable to connect
- auth failures after rotation or policy change

---

## 4) Incident workflow (lightweight)

### Step 1 — Detect
- alert from monitoring (heartbeat missing, KPI thresholds, error spikes)
- customer report / field report

### Step 2 — Triage
Capture:
- affected sites/devices (scope)
- severity (Sev1/Sev2/Sev3)
- start time and symptoms
- versions/variants involved (hardware/firmware/app)

### Step 3 — Mitigate / Restore service
Examples:
- remote restart of a component
- rollback to last known good version
- disable a risky feature flag/config
- temporary throttling or queue controls

### Step 4 — Document and create evidence
- key logs, metrics snapshots, correlation IDs
- timeline (what happened when)
- immediate fix/workaround

### Step 5 — RCA (root cause analysis) and follow-up decisions
- confirm root cause or most likely cause
- create prevention actions (backlog) where appropriate

---

## 5) When an incident becomes a backlog item (decision rules)

Create a backlog item when any of these is true:
1. **Recurrence:** the same issue happens ≥ 2 times in a defined period  
2. **High impact:** Sev1 or significant business impact even if one-off  
3. **High risk:** potential for widespread impact (e.g., cert expiry, rollout defect)  
4. **Preventable:** can be prevented through code/config/tooling/process change  
5. **Operational cost:** reduces field visits or significantly reduces MTTR

---

## 6) Backlog item types derived from incidents

### A) Root-cause fixes (engineering)
- bug fix (memory leak, reconnect logic, retry behavior)
- correctness fix (dedup/idempotency, schema handling)

### B) Observability improvements (ops + engineering)
- new metrics (queue depth, dropped events, event lag)
- better logs (structured logs, correlation IDs)
- dashboards and alert rules tuned for early detection

### C) Deployment safety improvements
- stronger pre-checks (variant matrix enforcement)
- staged rollout pause rules and automatic stop criteria
- improved rollback reliability

### D) Process/runbook improvements
- clearer escalation paths
- playbooks for common incident patterns
- operational thresholds and on-call readiness

---

## 7) Example mapping: incident → backlog

### Example 1 — LTE drops cause event loss
**Incident:** Several sites show missing events during LTE interruptions.  
**Mitigation:** Reconnect + manual resend.  
**Backlog items:**
- Implement persistent store-and-forward queue across reboots
- Add deduplication/idempotency keys
- Add queue depth monitoring + alert
- Improve reconnect/backoff logic

### Example 2 — Certificate expiry breaks connectivity
**Incident:** Devices fail to connect after certificate expires.  
**Mitigation:** Emergency manual certificate update on devices.  
**Backlog items:**
- Automated certificate rotation with grace period
- Alert on certificate expiry (e.g., 30/14/7 days)
- Audit logging for rotation events

### Example 3 — Rollout causes instability on one variant
**Incident:** After activation, Cam-B variants show stream instability.  
**Mitigation:** Rollback pilot cohort.  
**Backlog items:**
- Update variant matrix: block incompatible combo
- Add pre-check to prevent activation on Cam-B for this version
- Add SAT requirement for night conditions before pilot expansion

---

## 8) Metrics to track the effectiveness of incident-to-backlog work

- **MTTR trend:** average restoration time decreasing over time
- **Repeat incident rate:** fewer repeat incidents of the same type
- **Rollback rate:** fewer rollbacks per release (or faster, safer rollbacks)
- **Alert quality:** fewer false positives / earlier detection
- **Field visit count:** reduction in truck rolls for recurring issues

---

## 9) PO responsibilities (what I would own/drive)

A Product Owner typically:
- ensures recurring incidents are converted into clear backlog items
- prioritizes prevention work using impact/risk/operational cost
- aligns stakeholders on trade-offs (new capability vs reliability hardening)
- confirms acceptance criteria includes operability (metrics, alerts, rollback readiness)
- communicates trends (incidents, MTTR) and improvement plan to stakeholders

---

## 10) Practical checklist (copy/paste)
- [ ] Incident captured with timeline + affected cohort/variant
- [ ] Mitigation documented (what restored service)
- [ ] Evidence saved (logs/metrics/correlation IDs)
- [ ] RCA completed or next steps assigned
- [ ] Prevention backlog item created (if criteria met)
- [ ] Acceptance criteria include observability + rollback notes
- [ ] Variant matrix updated if compatibility is involved
