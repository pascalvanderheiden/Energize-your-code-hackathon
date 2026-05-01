---
agent: 'documenter'
description: 'Generate comprehensive project documentation including deployment guide, operational runbook, and updated README'
---

# Step 6: Generate Documentation

Generate comprehensive documentation for the complete project.

## Documentation to Create/Update

### 1. Update `iac/README.md`
- Add project overview with the architecture diagram from `iac/docs/architecture.md`
- Include the hackathon banner: `![Energize Your Code Banner](docs/images/hackathon-logo.png)`
- Add prerequisites, quick start guide, and project structure
- Link to all detailed documentation

### 2. Create `iac/docs/deployment-guide.md`
- Step-by-step deployment instructions (Azure CLI commands)
- Environment configuration (dev/staging/prod)
- Required Azure permissions
- Pre-deployment checklist
- Post-deployment verification
- Rollback procedures

### 3. Create `iac/docs/operations-runbook.md`
- Day-to-day operations guide
- Monitoring and alerting overview
- Common troubleshooting scenarios
- Scaling and backup procedures

### 4. Create `iac/docs/cost-estimation.md`
- Estimated monthly costs per resource
- Dev vs. prod cost comparison
- Cost optimization recommendations

## Source Documents

Read these existing documents for context:
- `iac/docs/architecture.md` — Architecture design
- `iac/docs/development-plan.md` — Implementation plan
- `iac/docs/test-results.md` — Test results (if available)
- `iac/infra/` — Actual infrastructure code
