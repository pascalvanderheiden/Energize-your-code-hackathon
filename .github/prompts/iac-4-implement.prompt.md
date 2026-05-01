---
agent: 'implementer'
description: 'Implement all infrastructure tasks from the development plan using Bicep with AVM modules'
---

# Step 4: Implement Infrastructure

Implement all tasks defined in `iac/docs/development-plan.md` using Bicep with Azure Verified Modules (AVM).

## Implementation Guide

1. Read `iac/docs/development-plan.md` for the complete task list
2. Read `iac/docs/architecture.md` for the target architecture and AVM module references
3. Implement tasks in dependency order:
   - **Layer 0**: Foundation (parameters, variables, shared config)
   - **Layer 1**: Networking (VNet, subnets, NSGs)
   - **Layer 2**: Data (SQL Server, SQL Database, Private DNS)
   - **Layer 3**: Compute (App Service Plan, Web App, VNet Integration)
   - **Layer 4**: Security & Connectivity (Private Endpoints, Managed Identity RBAC)
   - **Layer 5**: Monitoring (Log Analytics, App Insights — if in plan)
4. Use AVM modules with pinned versions for all resources
5. Validate each module with `az bicep build` after creation

## File Structure to Create

```
iac/infra/
├── main.bicep              # Main deployment orchestration
├── main.bicepparam         # Parameter file
├── modules/
│   ├── networking.bicep    # VNet, Subnets, NSGs
│   ├── database.bicep      # SQL Server, SQL Database, Private Endpoint, DNS
│   ├── webapp.bicep        # App Service Plan, Web App, VNet Integration
│   └── monitoring.bicep    # Log Analytics, App Insights
```

## Key Requirements

- All resources use AVM modules from `br/public:avm/res/`
- CAF naming conventions applied to all resource names
- Required tags on every resource: environment, workload, owner, costCenter
- SQL Server: `publicNetworkAccess: 'Disabled'`
- Web App: Managed Identity enabled, VNet integrated
- NSGs: Deny-all default with specific allow rules
