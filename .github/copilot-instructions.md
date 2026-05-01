# Energize Your Code Hackathon — Copilot Instructions

## Project Context

This hackathon project offers **three use cases** for teams to choose from. Each use case leverages GitHub Copilot's custom agents, skills, and MCP servers to build, discover, or design Azure infrastructure and applications.

### Use Case 1: Infrastructure as Code (IaC) — Greenfield Deployment
Build Azure infrastructure from scratch using **Bicep with Azure Verified Modules (AVM)** following **Cloud Adoption Framework (CAF)** and **Well-Architected Framework (WAF)** principles. Sequential agent workflow: architect → review → plan → implement → test → document → deploy → deploy app (sample only).

### Use Case 2: Agentic Discovery — Brownfield Assessment
Discover and analyse a **pre-deployed Azure environment** using the Azure MCP server. Assess the environment against WAF and CAF using the Learn MCP server. Produce comprehensive reports with Mermaid architecture diagrams, gap analysis, and actionable recommendations. Optionally create a PaaS migration plan with cost assessment.

### Use Case 3: Spec-Driven Development — Greenfield Application
Build a **greenfield web application** using spec-driven development methodology with **Spec Kit** or **OpenSpec**. Brainstorm an idea, discover and install skills, create specifications, implement, test with Playwright, and deploy to Azure. Focus is on the methodology and tooling process. Local development only — no Git/GitHub operations required.

## MCP Servers

Three MCP servers are pre-configured in `.vscode/mcp.json`:

| Server | Purpose | Used By |
|--------|---------|---------|
| **Azure MCP** | Azure resource management, discovery, deployment | All use cases |
| **Learn MCP** | Microsoft documentation, WAF/CAF references | Use Cases 1 & 2 |
| **Playwright MCP** | Browser automation and E2E testing | Use Case 3 |

## Use Case 1: IaC — Target Architecture

- **Azure Web App** (App Service) hosting a sample Todo application
- **Azure SQL Database** for persistent todo storage
- **Azure Virtual Network** with subnets for network isolation
- **Private Endpoint** connecting the Web App to SQL Database (no public DB access)
- **Network Security Groups (NSGs)** with explicit allow/deny traffic rules

## Use Case 2: Discovery — Assessment Criteria

When discovering and assessing an Azure environment, evaluate against:
- **WAF Pillars**: Rate each pillar (Reliability, Security, Cost, Operations, Performance) as ✅ Compliant, ⚠️ Partial, or ❌ Non-Compliant
- **CAF Compliance**: Check naming conventions, tagging strategy, resource organization
- **Security Posture**: Public endpoints, NSG rules, managed identities, TLS enforcement, secrets management
- **Network Isolation**: Private endpoints, VNet integration, subnet segmentation
- **Operational Maturity**: IaC coverage, monitoring, alerting, backup strategies

### Discovery Mermaid Diagram Standards
When generating architecture diagrams from discovered environments:
- Use `graph TB` for architecture overview, `graph LR` for network topology
- Group resources by resource group, then by VNet/subnet boundaries
- Color-code compliance: green for compliant, orange for partial, red for non-compliant
- Show all network connections including public endpoints (flagged as risks)
- Include resource names, types, and SKUs in node labels

## Use Case 3: Spec-Driven — Methodology Guidelines

### Supported Tools (participant's choice)
- **Spec Kit** (github/spec-kit): `specify init --here --ai copilot`
  - Commands: `/speckit.constitution`, `/speckit.specify`, `/speckit.plan`, `/speckit.tasks`, `/speckit.implement`
- **OpenSpec** (Fission-AI/OpenSpec): `npm install -g @fission-ai/openspec@latest && openspec init`
  - Commands: `/opsx:propose <idea>`, `/opsx:apply`, `/opsx:archive`

### Spec-Driven Workflow
1. **Brainstorm**: Define the application idea, user stories, and requirements
2. **Setup**: Search for skills (`npx skills find <domain>`), install tools, initialize spec-driven project
3. **Specify**: Create specifications using the chosen tool — focus on WHAT to build, not HOW
4. **Implement**: Let the AI build the application from specifications
5. **Test**: Write and run Playwright E2E tests to validate user stories
6. **Deploy**: Deploy to Azure using Azure MCP (App Service, Static Web Apps, or Container Apps)

### Feature Expansion (optional)
Instead of git branches, use folder-based feature isolation:
- **Spec Kit**: Set `SPECIFY_FEATURE` environment variable to the feature directory name
- **OpenSpec**: Each feature gets its own folder under `openspec/changes/`

## IaC Standards

### Azure Verified Modules (AVM)

- Always use AVM modules from the official Bicep registry: `br/public:avm/res/<provider>/<resource>:<version>`
- Prefer AVM resource modules (`avm/res/`) for individual resources
- Prefer AVM pattern modules (`avm/ptn/`) for common architecture patterns
- Pin module versions explicitly — never use `latest`
- Reference: https://aka.ms/AVM

### Naming Conventions (CAF)

Follow Azure CAF naming conventions:
- Resource Group: `rg-<workload>-<environment>-<region>`
- App Service: `app-<workload>-<environment>-<region>`
- SQL Server: `sql-<workload>-<environment>-<region>`
- SQL Database: `sqldb-<workload>-<environment>-<region>`
- Virtual Network: `vnet-<workload>-<environment>-<region>`
- Subnet: `snet-<purpose>-<environment>-<region>`
- NSG: `nsg-<purpose>-<environment>-<region>`
- Private Endpoint: `pep-<resource>-<environment>-<region>`
- Use lowercase, hyphens as separators, no underscores

### Tagging

All resources must include these tags:
- `environment`: dev, staging, prod
- `workload`: name of the application
- `owner`: team or individual responsible
- `costCenter`: billing code

## Well-Architected Framework (WAF) Pillars

When designing or reviewing infrastructure, consider all 5 pillars:

1. **Reliability**: Zone redundancy, health probes, retry policies, backup/restore
2. **Security**: Private endpoints, NSGs, managed identities (no passwords in code), TLS 1.2+, Azure Defender
3. **Cost Optimization**: Right-sized SKUs, auto-scaling, reserved instances where applicable
4. **Operational Excellence**: IaC for all resources, CI/CD pipelines, monitoring, alerting
5. **Performance Efficiency**: Appropriate SKU tiers, caching, connection pooling, CDN for static content

## Security Requirements

- Database must NOT be publicly accessible — use Private Endpoints only
- Use Managed Identity for App Service to SQL Database authentication where possible
- NSGs must follow least-privilege: deny all by default, allow only required traffic
- No secrets or connection strings in plain text — use Azure Key Vault or App Configuration
- Enable Azure Defender for SQL
- Enforce TLS 1.2 minimum on all resources

## File Structure

Each use case produces output in its own directory to avoid conflicts:

```
iac/                            # Use Case 1: IaC output
├── README.md                   # Project README
├── docs/
│   ├── architecture.md         # Architecture document with Mermaid diagrams
│   ├── architecture-review.md  # WAF/CAF review findings
│   ├── development-plan.md     # Task breakdown for implementation
│   ├── test-results.md         # Bicep validation results
│   ├── deployment-guide.md     # Deployment instructions
│   ├── operations-runbook.md   # Operations guide
│   ├── cost-estimation.md      # Cost estimates
│   └── images/                 # Diagrams and screenshots
├── infra/
│   ├── main.bicep              # Main deployment orchestration
│   ├── main.bicepparam         # Parameter file
│   └── modules/
│       ├── networking.bicep    # VNet, Subnets, NSGs
│       ├── database.bicep      # SQL Server, SQL Database, Private Endpoint
│       ├── webapp.bicep        # App Service Plan, Web App
│       └── monitoring.bicep    # Log Analytics, App Insights (optional)
├── app/                        # Sample Todo application (sample walkthrough only)
│   ├── Program.cs              # App entry point, API endpoints
│   ├── TodoApp.csproj          # Project file
│   ├── Models/                 # Entity models
│   ├── Data/                   # EF Core DbContext
│   └── wwwroot/                # Static assets

discovery/                      # Use Case 2: Discovery output
└── docs/
    ├── discovery-inventory.md  # Resource inventory
    ├── waf-assessment.md       # WAF/CAF compliance assessment
    ├── discovery-report.md     # Executive report with Mermaid diagrams
    └── migration-plan.md       # PaaS migration plan (optional)

# Use Case 3: Spec-Driven — folder structure is created by the tool:
# Spec Kit:  specs/<feature>/  (spec.md, plan.md, tasks.md, contracts/)
#            memory/            (constitution.md)
# OpenSpec:  openspec/changes/<feature>/  (proposals, specs, design, tasks)
```

## Bicep Formatting

- Use 2-space indentation
- Group parameters, variables, resources, and outputs in that order
- Add `@description()` decorators to all parameters
- Use `@secure()` for sensitive parameters
- Prefix output names with the module name for clarity
