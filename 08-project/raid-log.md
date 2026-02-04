# RAID log (Risks, Assumptions, Issues, Dependencies)

This document provides a lightweight RAID log structure for roadside delivery projects.
The goal is to make risks and dependencies visible early and manage them proactively.

---

## 1) How to use this
- Review weekly (or at each steering / release checkpoint)
- Keep entries short, owned, and actionable
- Tie mitigations to backlog items where possible

---

## 2) Definitions
- **Risk:** Something that might happen and negatively impact delivery/outcomes.
- **Assumption:** Something we believe to be true but has not been validated.
- **Issue:** Something that is happening now and needs resolution.
- **Dependency:** Something we rely on from another team/vendor/system to progress.

---

## 3) RAID table (example)

| ID | Type | Description | Impact | Likelihood | Owner | Mitigation / Next step | Due | Status |
|---:|:-----|:------------|:------:|:----------:|:------|:------------------------|:---:|:------:|
| R-01 | Risk | Connectivity instability on LTE sites may increase event lag and offline buffering | High | Medium | Ops Lead | Pilot on representative LTE sites; add queue depth alerts; validate retry/backoff | YYYY-MM-DD | Open |
| R-02 | Risk | Variant mismatch (firmware/app) could cause camera stream instability during rollout | High | Medium | System Eng | Enforce variant matrix pre-checks; block incompatible targets; SAT night testing | YYYY-MM-DD | Open |
| A-01 | Assumption | Backend ingestion API supports idempotency via event_id | High | Medium | Integration Lead | Confirm contract; add duplicate handling tests in Ring 0; document response behavior | YYYY-MM-DD | Open |
| A-02 | Assumption | Field access windows allow activation during maintenance hours | Medium | Medium | Field Lead | Confirm site access constraints per region; align rollout plan to windows | YYYY-MM-DD | Open |
| I-01 | Issue | Certificate expiry alerting not in place; risk of sudden disconnect | High | High | Security | Add cert expiry telemetry + alerts; implement rotation grace period | YYYY-MM-DD | In progress |
| D-01 | Dependency | Modem firmware update required before deploying Base Image 2.1 | Medium | Medium | Vendor Mgmt | Confirm lead time; plan phased upgrade; update variant matrix constraints | YYYY-MM-DD | Open |

**Impact/Likelihood scale (suggested):** Low / Medium / High

---

## 4) Good practices (PO-friendly)
- Convert mitigations into backlog items with clear acceptance criteria
- Make “stop criteria” explicit for rollout risks
- Track variant and field constraints as first-class risks
- Revisit assumptions early (validate before large commitments)
