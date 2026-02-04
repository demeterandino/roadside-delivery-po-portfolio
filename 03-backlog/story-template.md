# User story template (Roadside Delivery)

Use this template to keep stories consistent, testable, and rollout-ready in a field-deployed environment.

---

## US-XXX — <Short, outcome-focused title>

### Summary
As a <role/persona>, I want <capability>, so that <measurable benefit/outcome>.

### Background / Context
- Why now?
- What problem does this solve?
- Any prior incidents or known pain points?
- Assumptions and constraints (connectivity, field windows, etc.)

### Scope
**In scope**
- …

**Out of scope**
- …

### Acceptance Criteria (testable)
1. …
2. …
3. …

### Non-functional requirements (NFRs)
- **Reliability:** (uptime, buffering, retry, recovery expectations)
- **Operability:** (metrics, alerts, logs, diagnostics, runbook updates)
- **Security & compliance:** (access control, audit logging, data retention)
- **Performance:** (latency/throughput targets where relevant)
- **Compatibility:** (variants affected, backward compatibility expectations)

### Data / Interfaces (if applicable)
- API endpoints or message types:
- Schema/versioning notes:
- Idempotency/deduplication keys:
- Error handling and retry expectations:

### Rollout & rollback notes
- Target cohort: canary / pilot / wider rollout
- Stop criteria / pause rules:
- Rollback approach:
- Feature flags/config toggles (if used):

### Test approach
- Ring 0 (Lab/Staging):
- FAT:
- SAT:
- Pilot UAT:
- Hypercare monitoring:

### Dependencies & owners
- Dependency:
- Owner/team:
- Status:

### Definition of Done
- [ ] Acceptance criteria met
- [ ] Tests passed (Ring 0 + relevant gates)
- [ ] Observability in place (metrics/logs/alerts)
- [ ] Deployment notes documented (including rollback)
- [ ] Runbook / operational docs updated (if needed)
- [ ] Stakeholders informed (release notes updated)
