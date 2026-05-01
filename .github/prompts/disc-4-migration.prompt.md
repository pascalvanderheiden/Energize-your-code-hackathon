---
agent: 'migration-planner'
description: 'Create a PaaS migration plan with target architecture, phased roadmap, and cost assessment'
---

# Step 4 (Optional): Plan PaaS Migration

Design a migration plan to modernize the discovered environment from IaaS to Azure PaaS services.

## Input

- Read `discovery/docs/discovery-report.md` — executive report with current architecture
- Read `discovery/docs/waf-assessment.md` — compliance gaps to address in target design
- Read `discovery/docs/discovery-inventory.md` — detailed inventory of discovered resources and SKUs

## Migration Plan Requirements

### Target Architecture
- Map each IaaS resource to its PaaS equivalent (VM → App Service, SQL VM → Azure SQL, etc.)
- Apply all WAF/CAF best practices to the target design
- Use CAF naming conventions for all target resources
- Include private endpoints, managed identities, Key Vault integration

### Phased Roadmap (4 phases)
- **Phase 0: Foundation** — networking, DNS, Key Vault, monitoring
- **Phase 1: Data Migration** — databases, storage, private endpoints
- **Phase 2: Compute Migration** — apps, VNet integration, managed identity
- **Phase 3: Optimization** — security hardening, performance tuning, cost optimization

### Mermaid Diagrams
1. **Target-State Architecture** (`graph TB`) — proposed PaaS architecture
2. **Migration Phase Dependencies** (`graph LR`) — phase flow with dependencies

### Cost Assessment
- Current monthly cost estimate (from discovered SKUs)
- Target monthly cost estimate (PaaS pricing)
- Comparison table by category (compute, data, network, licensing)
- Expected savings percentage

### Risk Assessment
- Application compatibility risks
- Downtime requirements per phase
- Rollback strategy per phase

## Expected Output

Create `discovery/docs/migration-plan.md` containing:
1. Migration summary and target vision
2. Current-state architecture diagram
3. Target-state architecture diagram
4. Resource migration mapping table
5. Phased roadmap with timelines and dependencies
6. Migration phase dependency diagram
7. Risk register with mitigation strategies
8. Cost assessment with current vs. target comparison
9. Success criteria per phase
10. Rollback plan

## Next Step (Optional)

Hand off to `@reviewer` to validate the migration target architecture against WAF/CAF.
