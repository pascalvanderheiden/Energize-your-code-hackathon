---
agent: 'architect'
description: 'Extend the architecture with monitoring — Application Insights, Log Analytics, alerts, dashboards'
---

# Step 9 (OPTIONAL): Extend with Monitoring

Extend the existing architecture in `iac/docs/architecture.md` with comprehensive **monitoring and observability**.

## Monitoring Enhancements to Add

1. **Log Analytics Workspace** — Centralized log collection
2. **Application Insights** — APM for the Web App (request tracing, dependency tracking, exceptions)
3. **Diagnostic Settings** — Route SQL and App Service logs to Log Analytics
4. **Alerts** — Configure alert rules for:
   - Web App response time > 2 seconds
   - SQL Database DTU usage > 80%
   - HTTP 5xx error rate > 1%
   - Private endpoint connection health
5. **Dashboard** — Azure Dashboard with key metrics

## Expected Output

Update `iac/docs/architecture.md` with:
- Monitoring section with component diagram (Mermaid)
- Updated resource inventory including monitoring resources
- AVM module references for monitoring resources
- Additional implementation tasks
