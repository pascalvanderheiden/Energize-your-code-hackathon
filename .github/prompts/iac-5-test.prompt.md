---
agent: 'tester'
description: 'Execute all infrastructure tests — bicep build, linting, what-if deployment — and fix any errors until all pass'
---

# Step 5: Test Infrastructure

Execute all validation tests on the infrastructure code in `iac/infra/` and fix any errors until all checks pass.

## Test Execution Order

### 1. Syntax Validation
```bash
az bicep build --file iac/infra/modules/networking.bicep
az bicep build --file iac/infra/modules/database.bicep
az bicep build --file iac/infra/modules/webapp.bicep
az bicep build --file iac/infra/main.bicep
```

### 2. Linting
Review all warnings from bicep build output and resolve.

### 3. Security Validation
Verify in the code:
- [ ] SQL Server `publicNetworkAccess` is `'Disabled'`
- [ ] Web App has `identity.type: 'SystemAssigned'`
- [ ] NSGs include deny-all inbound rules
- [ ] Private endpoints configured for SQL
- [ ] No hardcoded secrets
- [ ] TLS 1.2+ enforced
- [ ] All resources tagged

### 4. What-If Deployment (if Azure is connected)
```bash
az deployment group what-if \
  --resource-group rg-todo-dev-westeurope \
  --template-file iac/infra/main.bicep \
  --parameters iac/infra/main.bicepparam
```

## Requirements

- Fix ALL errors — do not skip any failing test
- Re-run tests after each fix to verify
- Document all issues found and fixes applied
- Create `iac/docs/test-results.md` with the complete test report
