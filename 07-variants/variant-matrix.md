# Variant matrix (hardware + firmware + software compatibility)

This document shows a practical way to manage **hardware/software variants** for roadside products.
In a field-deployed environment, variants are normal (different device revisions, camera models, modems, regions).
A variant matrix helps prevent “works on one site, fails on another” rollouts.

> Note: The examples below are fictional and simplified.

---

## 1) What is a variant matrix?

A **variant matrix** is a controlled list of approved combinations of:
- **Hardware** (device model / revision, optional peripherals)
- **Firmware** (device firmware, camera firmware, router/modem firmware)
- **Software** (edge application version, OS/base image version)
- **Configuration baseline** (schema/version of config files)

It answers:
- “Which combinations are supported?”
- “Which release can be deployed to which sites?”
- “What is the baseline for acceptance (FAT/SAT/UAT) and operations?”

---

## 2) Why this matters for Roadside Delivery

Variant control directly reduces:
- rollout risk (unexpected incompatibilities),
- MTTR (faster diagnosis if baseline is known),
- field visits (fewer trial-and-error fixes),
- delayed acceptance (FAT/SAT failures due to mismatched versions).

It also supports:
- clear lifecycle management (EOL/EOS planning),
- predictable release and rollback strategies.

---

## 3) Example: variant catalogue (what we track)

### 3.1 Hardware categories (examples)
- Edge compute unit: model + hardware revision (e.g., HW-Rev A/B/C)
- Peripheral bundle: camera model, sensor model, RSU/radio module
- Network: router/modem model (LTE/5G) + firmware
- Storage: SSD size/type (if relevant)
- Power: PoE/UPS (if relevant)

### 3.2 Software categories (examples)
- Base image / OS version
- Edge application version
- Device management agent version
- Configuration schema version (e.g., config v1/v2)

---

## 4) Example: compatibility matrix (simplified)

Legend:
- ✅ Supported
- ⚠️ Supported with constraints (notes)
- ❌ Not supported

| Variant ID | Edge HW Rev | Camera Model | Modem FW | Base Image | Edge App | Config Schema | Status | Notes |
|-----------:|-------------|--------------|----------|-----------:|---------:|--------------:|:------:|------|
| V-101 | A | Cam-A | 1.2.x | 2.0 | 3.6 | v1 | ✅ | Baseline pilot set |
| V-102 | A | Cam-A | 1.2.x | 2.0 | 3.7 | v1 | ⚠️ | Requires DM-Agent ≥ 1.4 |
| V-103 | A | Cam-A | 1.2.x | 2.1 | 3.7 | v2 | ✅ | Approved after FAT update |
| V-201 | B | Cam-B | 2.0.x | 2.1 | 3.7 | v2 | ⚠️ | Night profile tuning needed in SAT |
| V-202 | B | Cam-B | 2.0.x | 2.1 | 3.8 | v2 | ❌ | Known issue: camera stream instability |
| V-301 | C | Cam-A | 2.1.x | 2.2 | 3.8 | v2 | ✅ | New hardware rev; limited rollout |

---

## 5) Governance: how variants are approved

### 5.1 Baselines
Define at least two baselines:
- **Operational baseline**: stable combo used for most sites (default)
- **Pilot baseline**: where new versions are validated first (canary/pilot)

### 5.2 Approval workflow (example)
1) Engineering proposes a new variant or version combination
2) Update is validated in Ring 0 (lab/staging)
3) FAT evidence updated (if required)
4) SAT performed for representative sites/conditions
5) Variant is marked as “approved” (✅) or “approved with constraints” (⚠️)
6) Rollout rules are updated (where this variant can be deployed)

### 5.3 “Approved with constraints” examples
- Allowed only for specific sites or time windows
- Requires specific configuration profile
- Requires monitoring thresholds adjusted for pilot
- Requires a firmware pre-upgrade step

---

## 6) Deployment rules derived from the matrix (practical)

### Rule examples
- “Edge App 3.8 can only be activated on variants V-101, V-103, V-301.”
- “Config schema v2 requires Base Image ≥ 2.1.”
- “Cam-B variants must complete SAT night testing before being added to pilot group.”

### Automated enforcement (ideal)
- Deployment tool blocks activation if target device variant is not compatible
- Pre-checks validate: firmware, base image, config schema, disk space, agent version

---

## 7) Lifecycle management (what changes over time)

### EOL/EOS (end-of-life / end-of-support)
A variant matrix supports lifecycle decisions:
- which hardware revisions are phased out,
- when a firmware line is no longer patched,
- how long backward compatibility must be maintained.

### Example lifecycle policy (simplified)
- Support N-1 major Edge App versions in production
- Support N-2 base images only for rollback, not for new deployments
- Firmware updates are grouped quarterly unless critical security fixes require a patch

---

## 8) PO responsibilities (how a Product Owner uses this)

A PO typically:
- ensures variant impacts are visible in backlog prioritization and release planning
- aligns stakeholders on which variants are “in scope” for a given milestone
- confirms acceptance gates cover representative variants (not just the easiest one)
- communicates constraints and rollout rules clearly (what can be deployed where)
- uses incidents and field feedback to refine the matrix and constraints

---

## 9) Suggested artifacts to keep alongside the matrix
- Variant catalogue (inventory export or reference list)
- Release notes with “supported variants” section
- Known issues list per variant
- SAT checklists per variant category
- Rollback compatibility notes (what version can safely roll back to what)  
