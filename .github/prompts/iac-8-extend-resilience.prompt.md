---
agent: 'architect'
description: 'Extend the architecture with WAF resilience patterns — zone redundancy, health probes, auto-scaling, failover'
---

# Step 8 (OPTIONAL): Extend with Resilience

Extend the existing architecture in `iac/docs/architecture.md` with **WAF Reliability pillar** enhancements.

## Resilience Enhancements to Add

1. **Zone Redundancy** — Enable zone-redundant deployment for App Service and SQL Database
2. **Health Probes** — Configure health check endpoints for the Web App
3. **Auto-Scaling** — Add auto-scale rules based on CPU/memory/request count
4. **Backup & Restore** — Configure automated SQL Database backups with point-in-time restore
5. **Retry Policies** — Document retry patterns for transient failures
6. **SLA Targets** — Define SLA targets and design for composite SLA

## Expected Output

Update `iac/docs/architecture.md` with:
- New resilience section with Mermaid diagrams
- Updated WAF pillar analysis reflecting resilience improvements
- Additional tasks for the development plan
