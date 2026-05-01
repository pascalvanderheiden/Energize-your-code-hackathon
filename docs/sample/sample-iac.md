# Sample Walkthrough: Use Case 1 — Infrastructure as Code

> **Don't want to build your own agentic template?** Follow this step-by-step guide to execute the pre-built IaC workflow using the example agents and prompts included in this repo.

---

## What You'll Use

| Asset | Location | Purpose |
|---|---|---|
| **Agents** | `.github/agents/` | `@architect`, `@reviewer`, `@planner`, `@implementer`, `@tester`, `@documenter`, `@deployer`, `@frontend-dev` |
| **Prompts** | `.github/prompts/` | `iac-1-architect` through `iac-7-deploy`, `iac-7.5-deploy-app` (+ optional `iac-8`, `iac-9`) |
| **Skills** | `.github/skills/` | `update-avm-modules-in-bicep`, `find-skills`, `dotnet-ui` |
| **MCP Servers** | `.vscode/mcp.json` | Azure MCP, Learn MCP, Bicep MCP (auto-enabled) |

## Agent Workflow

```
@architect → @reviewer → @planner → @implementer → @tester → @documenter → @deployer → @frontend-dev
```

---

## Prerequisites

Before starting, log in to Azure using the credentials provided by the hackathon coaches:

```bash
az login
az account show --query "{name:name, id:id, tenantId:tenantId}" -o table
```

> **Important**: Confirm the displayed subscription and tenant with the hackathon coaches before proceeding.

---

## Step 1: Design the Architecture

**Run prompt**: `iac-1-architect`

This invokes `@architect` to design a greenfield Azure infrastructure for a Todo application with:
- Azure Web App (App Service) on P1v3
- Azure SQL Database with Private Endpoint (no public access)
- Virtual Network with app and private endpoint subnets
- NSGs with deny-all default and least-privilege rules
- Managed Identity for App-to-SQL authentication
- All resources using AVM modules and CAF naming

**What it produces**: `docs/architecture.md` containing:
- Executive summary
- Mermaid architecture diagram (`graph TB`) and network topology diagram (`graph LR`)
- Full resource inventory with AVM module references
- WAF pillar analysis
- Security design details
- Tagging strategy and deployment approach

**Verify before continuing**:
- [ ] `docs/architecture.md` exists with Mermaid diagrams
- [ ] All resources follow CAF naming (e.g., `app-todo-dev-westeurope`)
- [ ] SQL Database uses Private Endpoint, no public access
- [ ] AVM modules are referenced with pinned versions

---

## Step 2: Review the Architecture

**Run prompt**: `iac-2-review`

This invokes `@reviewer` to audit `docs/architecture.md` against WAF and CAF:

**What it produces**: `docs/architecture-review.md` containing:
- Health score (0-100%)
- WAF compliance matrix (5 pillars rated ✅/⚠️/❌)
- Security findings categorized as Critical/High/Medium/Low
- CAF compliance check (naming, tagging, resource organization)
- Prioritized recommendations (P1-P4)

**Verify before continuing**:
- [ ] Review document exists with health score
- [ ] All 5 WAF pillars are assessed
- [ ] No Critical security findings remain unaddressed
- [ ] Naming and tagging follow CAF conventions

---

## Step 3: Create the Development Plan

**Run prompt**: `iac-3-plan`

This invokes `@planner` to break the architecture into ordered implementation tasks:

**What it produces**: `docs/development-plan.md` containing:
- Task count and implementation layers (Layer 0-7)
- Mermaid dependency graph showing task relationships
- Tasks grouped by layer: Foundation → Networking → Data → Compute → Security → Monitoring → CI/CD → Testing
- Each task has: ID, title, file path, dependencies, acceptance criteria
- Parallel execution guide

**Example task layers**:
| Layer | Tasks | Description |
|---|---|---|
| L0 | Main Bicep scaffold | `infra/main.bicep`, `infra/main.bicepparam` |
| L1 | Networking module | VNet, subnets, NSGs |
| L2 | Database module | SQL Server, SQL Database, Private DNS |
| L3 | Compute module | App Service Plan, Web App |
| L4 | Security & connectivity | Private Endpoint, VNet integration, RBAC |
| L5 | Monitoring (optional) | Log Analytics, App Insights |
| L6 | CI/CD workflows | PR validation, deployment pipeline |
| L7 | Testing | Bicep build, what-if, security checklist |

**Verify before continuing**:
- [ ] Plan exists with dependency graph
- [ ] Tasks are ordered by dependency layer
- [ ] No circular dependencies

---

## Step 4: Implement the Infrastructure

**Run prompt**: `iac-4-implement`

This invokes `@implementer` to write all Bicep modules following the development plan:

**What it produces**:
```
infra/
├── main.bicep              # Orchestrates all modules
├── main.bicepparam         # Parameter values for dev environment
└── modules/
    ├── networking.bicep    # VNet, subnets, NSGs (AVM)
    ├── database.bicep      # SQL Server, DB, Private Endpoint, DNS (AVM)
    ├── webapp.bicep         # App Service Plan, Web App, VNet integration (AVM)
    └── monitoring.bicep    # Log Analytics, App Insights (AVM, optional)
```

**Key implementation details**:
- All resources use AVM modules from `br/public:avm/res/<provider>/<resource>:<version>`
- SQL Server has `publicNetworkAccess: 'Disabled'`
- Web App uses `SystemAssigned` Managed Identity
- NSGs have explicit deny-all inbound with specific allow rules
- All resources tagged with `environment`, `workload`, `owner`, `costCenter`

**Verify before continuing**:
- [ ] All module files exist in `infra/modules/`
- [ ] `main.bicep` references all modules
- [ ] AVM module versions are pinned (not `latest`)
- [ ] No secrets or connection strings in plain text

---

## Step 5: Test the Infrastructure

**Run prompt**: `iac-5-test`

This invokes `@tester` to validate all Bicep code:

**What it runs**:
1. `az bicep build` on each module and `main.bicep`
2. Lint warnings check
3. Security validation:
   - SQL `publicNetworkAccess: Disabled` ✓
   - Managed Identity configured ✓
   - NSGs applied to all subnets ✓
   - Private Endpoints present ✓
   - No secrets in code ✓
   - TLS 1.2+ enforced ✓
   - All tags present ✓
4. What-if deployment: `az deployment group what-if`

**What it produces**: `docs/test-results.md` with pass/fail for each check

The tester will **automatically fix errors** and re-run until all tests pass.

**Verify before continuing**:
- [ ] `az bicep build` passes on all files
- [ ] No security validation failures
- [ ] What-if shows expected resources (no surprises)

---

## Step 6: Generate Documentation

**Run prompt**: `iac-6-document`

This invokes `@documenter` to create project documentation:

**What it produces**:
- Updated `README.md` with architecture overview, Mermaid diagram, prerequisites, quick start
- `docs/deployment-guide.md` — step-by-step deployment instructions, environment configuration, permissions, verification checklist, rollback procedure
- `docs/operations-runbook.md` — daily operations, monitoring, troubleshooting, scaling, backup/restore
- `docs/cost-estimation.md` — monthly cost breakdown, dev vs prod comparison, optimization tips

**Verify before continuing**:
- [ ] README has architecture diagram and quick start
- [ ] Deployment guide has clear step-by-step instructions
- [ ] Cost estimation is realistic for the selected SKUs

---

## Step 7: Deploy to Azure

**Run prompt**: `iac-7-deploy`

This invokes `@deployer` to deploy everything to Azure:

**What it does**:
1. Authenticates with Azure CLI (`az login`)
2. Creates resource group: `rg-todo-dev-westeurope`
3. Runs what-if preview for final confirmation
4. Deploys `infra/main.bicep` with parameters
5. Verifies all resources:
   - Web App is running and accessible via HTTPS
   - SQL Database has no public access
   - Private Endpoint is connected and approved
   - NSGs are applied to both subnets
   - Managed Identity is assigned
   - All tags are present

**Verify after deployment**:
- [ ] Resource group `rg-todo-dev-westeurope` exists
- [ ] Web App responds on HTTPS
- [ ] SQL Server shows `publicNetworkAccess: Disabled` in portal
- [ ] Private Endpoint status is `Approved`
- [ ] All resources have required tags

---

## Step 7.5: Deploy a Sample Todo Application

**Run prompt**: `iac-7.5-deploy-app`

This invokes `@frontend-dev` to build and deploy a working Todo application to the existing Azure Web App, with data stored in the SQL Database via Managed Identity.

> **Skill**: The `dotnet-ui` skill is used for .NET UI design patterns when building the Todo frontend.

**What it does**:
1. Scaffolds a .NET 8 ASP.NET Core Todo app under `iac/app/`
2. Configures EF Core with the `SqlConnection` connection string (Managed Identity — no passwords)
3. Creates API endpoints: `GET/POST/PUT/DELETE /api/todos`
4. Builds a simple, clean Todo UI page
5. Adds a `/health` endpoint (matches the Web App health check configuration)
6. Publishes and deploys the app to Azure Web App via `az webapp deploy`
7. Grants the Managed Identity SQL database roles (`db_datareader`, `db_datawriter`, `db_ddladmin`)
8. Auto-migrates the database schema on startup via `Database.Migrate()`
9. Verifies end-to-end: Todo UI loads, CRUD operations work, data persists in SQL

**What it produces**:
```
iac/app/
├── Program.cs              # App entry point, API endpoints, service config
├── TodoApp.csproj          # Project file with EF Core + Azure.Identity
├── Models/
│   └── TodoItem.cs         # Todo entity (Id, Title, IsComplete, CreatedAt)
├── Data/
│   └── TodoDbContext.cs    # EF Core context for SQL
└── wwwroot/                # Static assets (CSS, JS)
```

Plus a **live, working Todo application** on the deployed Azure Web App with data persisted in Azure SQL.

**Verify after deployment**:
- [ ] Web App serves the Todo application on HTTPS
- [ ] Can create, read, update, and delete todo items
- [ ] Data is persisted in Azure SQL Database
- [ ] `/health` endpoint returns 200 OK
- [ ] `/api/todos` returns JSON response
- [ ] No passwords or secrets in application code
- [ ] Managed Identity authentication is used for SQL

---

## Optional Extensions

### Step 8: Add Resilience (`iac-8-extend-resilience`)

Extends the architecture with:
- Zone redundancy for App Service and SQL
- Health probes and auto-scaling rules
- Automated SQL backups with Point-in-Time Restore
- Retry policies and SLA targets

### Step 9: Add Monitoring (`iac-9-extend-monitoring`)

Extends the architecture with:
- Log Analytics workspace
- Application Insights (APM)
- Diagnostic settings on all resources
- Alerts: response time >2s, DTU >80%, 5xx errors >1%
- Azure Dashboard

---

## Expected Final Output

After completing all steps, your repo should contain:

```
infra/
├── main.bicep
├── main.bicepparam
└── modules/
    ├── networking.bicep
    ├── database.bicep
    ├── webapp.bicep
    └── monitoring.bicep
app/
├── Program.cs
├── TodoApp.csproj
├── Models/
│   └── TodoItem.cs
├── Data/
│   └── TodoDbContext.cs
└── wwwroot/
docs/
├── architecture.md
├── architecture-review.md
├── development-plan.md
├── test-results.md
├── deployment-guide.md
├── operations-runbook.md
└── cost-estimation.md
```

Plus a deployed Azure environment with all resources running, verified, and a **working Todo application** demonstrating end-to-end connectivity.
