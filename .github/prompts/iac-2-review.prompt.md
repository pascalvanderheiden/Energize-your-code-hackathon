---
agent: 'reviewer'
description: 'Review the proposed architecture against Azure WAF and CAF principles, identify gaps and provide recommendations'
---

# Step 2: Review Architecture

Review the proposed architecture documented in `iac/docs/architecture.md`.

## Review Scope

1. **WAF Compliance** — Evaluate all 5 pillars (Reliability, Security, Cost Optimization, Operational Excellence, Performance Efficiency) with compliance ratings
2. **CAF Compliance** — Validate naming conventions, tagging strategy, and resource organization
3. **Security Deep-Dive** — Verify private endpoints, NSGs, managed identities, TLS enforcement, no public DB access
4. **AVM Module Validation** — Confirm AVM modules are used correctly with pinned versions

## Expected Output

Create `iac/docs/architecture-review.md` containing:
1. Review summary with health score (0-100%)
2. WAF compliance matrix with ratings per pillar
3. Security findings (Critical/High/Medium/Low)
4. Prioritized recommendations (P1-P4)
5. Questions for the team where design is ambiguous
