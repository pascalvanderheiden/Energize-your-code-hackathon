---
agent: 'reviewer'
description: 'Assess the discovered Azure environment against WAF pillars and CAF conventions using the Learn MCP server'
---

# Step 2: Assess WAF/CAF Compliance

Read the discovery inventory and assess the environment against Microsoft's Well-Architected Framework and Cloud Adoption Framework.

## Input

Read `discovery/docs/discovery-inventory.md` — the resource inventory produced by the discoverer.

## Assessment Scope

### WAF Pillars (rate each as ✅ Compliant, ⚠️ Partial, or ❌ Non-Compliant)
1. **Reliability** — zone redundancy, health probes, backup/restore, failover, auto-scaling
2. **Security** — network isolation, public endpoints, NSGs, managed identity, TLS, secrets, Defender
3. **Cost Optimization** — SKU sizing, auto-scaling, reserved instances, orphaned resources
4. **Operational Excellence** — IaC coverage, monitoring, diagnostics, alerting, tagging
5. **Performance Efficiency** — SKU appropriateness, caching, connection pooling, regional placement

### CAF Compliance
- **Naming conventions** — audit all resource names against CAF patterns
- **Tagging** — check for required tags: environment, workload, owner, costCenter
- **Resource organization** — logical resource group structure

### Security Findings (prioritize as P1-P4)
- P1 Critical: public database access, plain text secrets, no NSGs
- P2 High: missing network isolation, no managed identity, no monitoring
- P3 Medium: naming violations, missing tags, no auto-scaling
- P4 Low: SKU optimization, missing documentation

## Expected Output

Create `discovery/docs/waf-assessment.md` containing:
1. Assessment summary with overall health score
2. WAF compliance matrix (5 pillars with ratings)
3. Detailed analysis per pillar with findings and remediation steps
4. CAF compliance report (naming + tagging audit)
5. Prioritized security findings (P1-P4)
6. Top 10 recommendations

## Next Step

After assessment is complete, hand off to `@reporter` for report synthesis and Mermaid diagrams.
