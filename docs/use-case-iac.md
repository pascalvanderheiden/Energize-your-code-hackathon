# Use Case 1: Infrastructure as Code (IaC) — Greenfield Deployment

Build Azure infrastructure from scratch using Bicep with Azure Verified Modules (AVM), following Cloud Adoption Framework (CAF) and Well-Architected Framework (WAF) principles.

## Objective

Design, build, and execute your own **agentic workflow template** — a set of custom agents, prompts, skills, and MCP server configurations — to deploy a complete Azure infrastructure from scratch. Your team decides WHAT to build and HOW to orchestrate the AI agents.

## Prerequisites

- Azure subscription with Contributor access
- Azure CLI with Bicep installed (`az bicep install`)
- VS Code with GitHub Copilot (or Codespaces)

### Azure Login (Required First)

Log in to Azure using the credentials provided by the hackathon coaches:

```bash
az login
```

Verify you are on the correct subscription:

```bash
az account show --query "{name:name, id:id, tenantId:tenantId}" -o table
```

> **Important**: Confirm the displayed subscription and tenant with the hackathon coaches before proceeding with any exercises.

---

## Participant Guide

### Phase 1: Define What You Want to Deploy

Answer these questions as a team:

- [ ] **What workload are you building?** (e.g., a Todo app, an API backend, a microservice)
- [ ] **What Azure resources do you need?** List at minimum:
  - A compute resource (App Service, Container Apps, Functions, etc.)
  - A database with private connectivity (SQL, PostgreSQL, Cosmos DB, etc.)
  - Virtual Network with subnet isolation
  - Private Endpoints — no public database access
  - NSGs with explicit allow/deny traffic rules
- [ ] **What region and environment?** (e.g., `westeurope`, `dev`)
- [ ] **What naming convention will you follow?** (CAF recommends `<type>-<workload>-<env>-<region>`)

### Phase 2: Define What You Need (Tools & MCP Servers)

Think about which tools your agents will need to do their job:

- [ ] **Which MCP servers do you need?**
  - Azure MCP — for deploying and managing resources?
  - Learn MCP — for referencing WAF/CAF documentation?
  - Bicep MCP — auto-enabled via the Bicep VS Code extension
- [ ] **Which skills do you need?**
  - Are there existing skills you can use? (check `.github/skills/` or run `npx skills find <domain>`)
  - Do you need a skill for AVM module discovery? Terraform style? Something else?
- [ ] **Configure your MCP servers** in `.vscode/mcp.json`

### Phase 3: Design Your Agentic Workflow

Plan the agents that will execute your pipeline. Think about:

- [ ] **What steps does your workflow need?** (e.g., design → review → plan → implement → test → deploy)
- [ ] **What agent handles each step?** Define a custom agent (`.github/agents/<name>.agent.md`) for each role
- [ ] **What tools does each agent need?** (MCP servers, VS Code extensions)
- [ ] **How do agents hand off to each other?** Define handoffs between agents
- [ ] **What does each agent output?** (e.g., a markdown doc, Bicep files, test results)

For each agent, create a `.agent.md` file in `.github/agents/` with:
- A clear role description
- The tools it needs access to
- Handoffs to the next agent in the workflow
- Detailed instructions on what it should produce

> **Built-in help**: When you create or edit a `.agent.md` file, Copilot automatically loads the [agents instruction file](../.github/instructions/agents.instructions.md) with guidelines on frontmatter, tools, handoffs, and best practices.

### Phase 4: Write Your Prompts

For each step in your workflow, create a reusable prompt (`.github/prompts/<name>.prompt.md`):

- [ ] **What should each prompt ask the agent to do?** Be specific about inputs and expected outputs
- [ ] **What context does each prompt need?** (files to read, parameters to fill in)
- [ ] **Number your prompts sequentially** so they're easy to run in order

> **Built-in help**: When you create or edit a `.prompt.md` file, Copilot automatically loads the [prompt instruction file](../.github/instructions/prompt.instructions.md) with guidelines on frontmatter, structure, inputs, and output definitions.

### Phase 5: Execute Your Workflow

Run your prompts in order and verify each step:

1. **Design** — Run your architecture prompt. Verify the output includes Mermaid diagrams, resource list, AVM modules
2. **Review** — Run your review prompt. Verify WAF/CAF compliance is assessed
3. **Plan** — Run your planning prompt. Verify tasks are broken down with dependencies
4. **Implement** — Run your implementation prompt. Verify Bicep modules are created
5. **Test** — Run your test prompt. Verify `bicep build` passes, lint checks succeed
6. **Document** — Run your documentation prompt. Verify deployment guide is created
7. **Deploy** — Run your deploy prompt. Verify resources are deployed to Azure

### Phase 6: Verify & Iterate

After deployment, check:
- [ ] Compute resource is accessible via HTTPS
- [ ] Database has no public access (Private Endpoint only)
- [ ] Private endpoint is connected and approved
- [ ] NSG rules are applied to all subnets
- [ ] All resources follow CAF naming conventions
- [ ] All resources have required tags (environment, workload, owner, costCenter)
- [ ] Managed Identity is used (no passwords)

---

## IaC Standards

### Azure Verified Modules (AVM)
- Use modules from `br/public:avm/res/<provider>/<resource>:<version>`
- Pin versions explicitly — never use `latest`

### CAF Naming Conventions
- Resource Group: `rg-<workload>-<environment>-<region>`
- App Service: `app-<workload>-<environment>-<region>`
- SQL Server: `sql-<workload>-<environment>-<region>`
- Virtual Network: `vnet-<workload>-<environment>-<region>`
- NSG: `nsg-<purpose>-<environment>-<region>`

### Required Tags
All resources must include: `environment`, `workload`, `owner`, `costCenter`

### Security Requirements
- Database must NOT be publicly accessible — Private Endpoints only
- Managed Identity for authentication — no passwords in code
- NSGs with least-privilege rules
- TLS 1.2+ enforced on all resources
- No secrets in plain text — use Key Vault

## Expected File Structure

```
.github/
├── agents/                 # 👈 Your custom agents
│   ├── <your-agent>.agent.md
│   └── ...
├── instructions/           # 🤖 Auto-activating Copilot guidelines (pre-configured)
│   ├── agents.instructions.md
│   ├── prompt.instructions.md
│   ├── agent-skills.instructions.md
│   └── instructions.instructions.md
├── prompts/                # 👈 Your reusable prompts
│   ├── <your-prompt>.prompt.md
│   └── ...
├── skills/                 # 👈 Your custom skills (if any)
│   └── ...
└── copilot-instructions.md # 👈 Workspace-wide context
.vscode/
└── mcp.json                # 👈 Your MCP server configuration
infra/
├── main.bicep              # Main deployment orchestration
├── main.bicepparam         # Parameter file
└── modules/
    ├── networking.bicep    # VNet, Subnets, NSGs
    ├── database.bicep      # SQL Server, SQL Database, Private Endpoint
    ├── webapp.bicep        # App Service Plan, Web App
    └── monitoring.bicep    # Log Analytics, App Insights (optional)
docs/
├── architecture.md         # Architecture document with Mermaid diagrams
├── development-plan.md     # Task breakdown for implementation
└── images/
```

---

## Stuck? Check the Examples

This repo includes a **complete reference implementation** showing one way to solve this use case:

| What | Where | Purpose |
|---|---|---|
| Example agents | `.github/agents/architect.agent.md`, `reviewer.agent.md`, etc. | See how agents are defined with tools, handoffs, and instructions |
| Example prompts | `.github/prompts/iac-1-architect.prompt.md`, etc. | See how prompts are structured for each workflow step |
| Example skills | `.github/skills/update-avm-modules-in-bicep/` | See how skills provide domain knowledge |
| Instruction files | `.github/instructions/` | Auto-loaded guidelines for writing agents, prompts, skills, and instructions |
| Example MCP config | `.vscode/mcp.json` | See how MCP servers are configured |

> **These are just examples** — your team should build your own agents, prompts, and workflow. Use the examples for inspiration when you get stuck.

---

## Tips

- **Start with architecture** — Everything else depends on knowing what you're building
- **Build agents incrementally** — Create one agent, test it, then build the next
- **Review before implementing** — A review step catches issues early and saves time
- **Design your own workload** — The Todo app is just one option; be creative
- **Work in parallel** — After the plan, team members can implement different modules
- **Test early** — Run `bicep build` after each module to catch errors immediately
