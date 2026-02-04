# US-003 — Staged rollout (canary → pilot → wider rollout) with stop criteria and rollback

## Summary
As a Release Manager / Operations Lead, I want a staged rollout mechanism (canary → pilot → wider rollout) with clear
stop criteria and a tested rollback path, so that we can deploy updates to roadside devices safely and predictably.

## Background / Context
Roadside devices are field-deployed and connectivity can be intermittent. A defect can cause outages, data loss, and increased
operational cost. A staged rollout reduces blast radius and provides early signals before expanding to more sites.

This story covers:
- how a release is introduced to a small cohort,
- what we monitor,
- when we pause,
- and how we roll back.

## Scope
### In scope
- Define rollout cohorts (canary group, pilot group)
- Deployment workflow: **download → verify → activate**
- KPI-driven health checks and **pause rules**
- Rollback procedure to previous stable version
- Audit logging for all rollout actions

### Out of scope
- Full UI design of dashboards (assumed to exist; only requirements included)
- Major refactoring of the deployment platform (use existing capabilities where possible)

## Acceptance Criteria

### A) Cohort management
1. The system supports defining device groups:
   - **Canary group** (small, low-risk set)
   - **Pilot group** (representative set)
2. A device can belong to only one active rollout cohort for a given release.
3. Cohort membership changes are audited (who/when/what).

### B) Deployment workflow (download → verify → activate)
4. Release can be deployed in two steps:
   - **Download only** (no service impact)
   - **Activate** (service impact possible)
5. Before activation, the device verifies package integrity (checksum/signature).
6. Activation can be scheduled within a configurable maintenance window.
7. Device reports deployment state transitions:
   - pending → downloading → verified → activated OR failed

### C) Health checks and stop criteria (pause rules)
8. During canary and pilot, the rollout automatically pauses if any of the following occurs (example rules):
   - availability drops below agreed threshold for the cohort
   - event lag exceeds threshold for a sustained period
   - dropped events exceed threshold for a sustained period
   - Sev1 incident is opened and linked to the release
   - rollback rate exceeds threshold in the cohort
9. Pause events include an explanation and links to relevant metrics/logs.

### D) Rollback
10. Rollback to the previous stable version is supported for devices in the cohort.
11. Rollback completes within an agreed time target for a device (e.g., X minutes) OR raises an alert.
12. After rollback, the device returns to “healthy” state or is flagged for investigation.
13. Rollback actions are audited (who/when/why) and reflected in rollout status.

### E) Observability requirements (minimum)
14. The following are available per device and per cohort:
   - current version (base image, app, agent where applicable)
   - last heartbeat time
   - deployment state
   - key KPIs (availability, event lag, dropped events)
15. Logs and diagnostics for failed deployments can be collected remotely (or linked to an existing log collection process).

## Non-functional Requirements
- **Reliability:** rollout must minimize downtime and avoid mass failure.
- **Security:** only authorized roles can start/pause/rollback; actions are audited.
- **Operability:** clear status reporting and actionable failure reasons.
- **Compatibility:** rollout respects the variant matrix and blocks incompatible targets.

## Test Approach
### Ring 0 (Lab/Staging)
- Simulate a rollout to test devices and verify state transitions.
- Simulate network drop during download and activation; validate retry behavior.
- Verify pause rules trigger under synthetic KPI degradation.
- Verify rollback returns device to previous stable state.

### FAT (if required)
- Demonstrate staged rollout workflow and rollback evidence.

### SAT / Pilot validation
- Run canary on one low-risk site/device, monitor KPIs for the agreed window.
- Expand to pilot cohort only after canary exit criteria are met.

## Rollout Plan (example)
1. **Canary**: 1 site/device for 48 hours
2. **Pilot**: 5–10 sites/devices for 7–14 days (representative conditions)
3. **Wider rollout**: phased cohorts by region/corridor after pilot success

## Go/No-Go KPIs (illustrative)
- Availability (cohort): ≥ 99.5%
- Event lag: median < 2s, p95 < 10s
- Dropped events: < 0.1%
- Rollback rate: < 5% in cohort
- Incidents: no Sev1 attributable to the release during observation window

## Dependencies
- Device Management platform supports group targeting and state reporting
- Monitoring dashboards provide required KPIs
- Variant matrix exists and is consulted during deployment targeting

## Definition of Done
- Cohorts can be created and managed
- Rollout workflow works end-to-end in Ring 0
- Pause rules and rollback are tested and documented
- Required observability and audit logs are in place
- Release notes include supported variants and rollout plan
