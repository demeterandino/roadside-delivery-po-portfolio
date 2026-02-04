# Context diagram (Roadside Delivery)

This document describes a **high-level system context** for a typical roadside delivery product.
It is intentionally simplified to focus on interfaces, responsibilities, and delivery/operability concerns.

---

## 1) System context (high-level)

```text
+-------------------+       TLS/VPN over IP       +----------------------+
|  Roadside Site    |  (LTE / Fiber connectivity) |   Central Platforms  |
|-------------------|---------------------------->|----------------------|
| - Sensors/Cameras |                             |  Device Management   |
| - RSU/Radio       |                             |  - telemetry/alerts  |
| - Edge Compute    |                             |  - config mgmt       |
| - Local Storage   |                             |  - deploy/rollback   |
| - Router/Modem    |                             |  - logs/diagnostics  |
+-------------------+                             |                      |
        |                                         |  Business Backend    |
        | field access                            |  - event ingestion   |
        v                                         |  - processing/rules  |
+-------------------+                             |  - reporting/audit   |
| Field Engineering |                             +----------------------+
| install/commission|
| maintenance       |
+-------------------+


### Two key data flows
1) **Operational flow** (for running the system)
- telemetry/metrics/heartbeat → Device Management
- commands/config/deployments ← Device Management

2) **Business flow** (for generating business value)
- events (e.g., detections/transactions/messages) → Business Backend
- acknowledgements/errors ← Business Backend

---

## 2) Core building blocks (plain language)

### Roadside Site (edge)
**What it is:** A physical installation near the road with devices and an edge computer.  
**Typical responsibilities:**
- detect/collect signals (camera/sensor/radio)
- do local processing (pre-validation, packaging, buffering)
- maintain local store-and-forward (when connectivity is down)
- run self-checks and report health telemetry
- receive config and software updates

### Connectivity (LTE/Fiber + security)
**What it is:** The network path from site to central systems.  
**Reality check:** connectivity may be intermittent → retries, buffering, and idempotency matter.

### Device Management (operations platform)
**What it is:** The “control room” for devices.  
**Responsibilities:**
- device inventory, versions, and configuration state
- telemetry, dashboards, and alerting
- deployment orchestration (download → verify → activate)
- rollback execution
- logs/diagnostics collection for troubleshooting

### Business Backend (domain platform)
**What it is:** Systems that ingest roadside events and turn them into business outcomes.  
**Responsibilities (generic):**
- stable event ingestion interface (API/contract)
- processing rules, validation, deduplication
- reporting, audit support, retention policies

### Field Engineering (installation & commissioning)
**What it is:** People/processes to install and “make it real” on the physical site.  
**Responsibilities:**
- install hardware, verify power/network
- calibrate/align devices and apply site configuration
- execute site acceptance checklist and hand over to operations

---

## 3) System boundaries & ownership (PO-friendly view)

### Roadside Product scope (what we consider “in”)
- Edge application(s) running on the roadside compute unit
- Configuration model and validation
- Deployment/rollback mechanism and versioning rules
- Telemetry/metrics/logging contract (what we measure and how)
- Compatibility rules across hardware/firmware/software variants

### Out of scope (but strong dependencies)
- Civil works (gantries, poles, road closures) → project/field planning
- Customer backoffice details (exact processing rules) → integration dependency
- Telecom provider performance (LTE coverage) → environment dependency

---

## 4) Key non-functional assumptions (what drives delivery choices)
- **Reliability:** devices must run unattended for long periods
- **Operability:** remote diagnosis is cheaper than field visits (truck rolls)
- **Security:** secure comms (TLS/VPN), controlled access, auditable actions
- **Intermittent connectivity:** store-and-forward + retry policies required
- **Mixed versions during rollout:** backward compatibility and safe activation needed

---

## 5) Common risks to highlight early
- Connectivity instability → event lag/dropped events if buffering/dedup is weak
- Hardware variance → “works on one variant, fails on another” unless managed
- Field constraints (access windows, permits, weather) → schedule risks
- Incomplete observability → slow incident resolution and higher MTTR (Mean Time to Recover)
- Interface changes without versioning → breaking changes during rollout
