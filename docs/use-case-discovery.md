# Use Case 2: Agentic Discovery — Brownfield Assessment

Discover and analyse a live Azure environment using custom agents, skills, and MCP servers.

## Objective

Design, build, and execute your own **agentic discovery workflow** — a set of custom agents, prompts, skills, and MCP server configurations — to scan, assess, and report on existing Azure infrastructure. Your team decides HOW to structure the discovery, WHAT to assess, and HOW to present the findings.

## Prerequisites

- Azure subscription with a deployed environment (you'll provide a resource group to scan)
- Azure CLI authenticated (`az login`)
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

### Phase 1: Define What You Want to Discover

Answer these questions as a team:

- [ ] **What Azure subscription and resource group will you scan?**
- [ ] **What information do you need to collect?** (resource types, configurations, networking, tags, security settings)
- [ ] **What framework will you assess against?** (WAF pillars, CAF conventions, security baselines)
- [ ] **What deliverables do you want to produce?**
  - Resource inventory?
  - WAF compliance assessment?
  - Architecture diagrams (Mermaid)?
  - Migration plan?
  - Executive report?

### Phase 2: Define What You Need (Tools & MCP Servers)

Think about which tools your agents will need:

- [ ] **Which MCP servers do you need?**
  - Azure MCP — for scanning and querying deployed resources
  - Learn MCP — for referencing WAF/CAF documentation from Microsoft Learn
- [ ] **Which VS Code extensions might help?**
  - Mermaid — for generating and validating architecture diagrams
- [ ] **Which skills do you need?**
  - Are there existing skills in `.github/skills/` you can use?
  - Do you need custom skills for assessment criteria or reporting templates?
- [ ] **Configure your MCP servers** in `.vscode/mcp.json`

### Phase 3: Design Your Agentic Workflow

Plan the agents that will execute your discovery pipeline:

- [ ] **What steps does your workflow need?** (e.g., discover → assess → report → plan migration)
- [ ] **What agent handles each step?** Define a custom agent (`.github/agents/<name>.agent.md`) for each role
- [ ] **What tools does each agent need?** (Azure MCP for scanning, Learn MCP for documentation, Mermaid for diagrams)
- [ ] **How do agents hand off to each other?** (e.g., discovery output feeds into assessment)
- [ ] **What does each agent output?** (e.g., inventory markdown, assessment report, Mermaid diagrams)

For each agent, create a `.agent.md` file in `.github/agents/` with:
- A clear role description
- The tools it needs access to
- Handoffs to the next agent in the workflow
- Detailed instructions on what it should discover, assess, or report

> **Built-in help**: When you create or edit a `.agent.md` file, Copilot automatically loads the [agents instruction file](../.github/instructions/agents.instructions.md) with guidelines on frontmatter, tools, handoffs, and best practices.

### Phase 4: Write Your Prompts

For each step in your workflow, create a reusable prompt (`.github/prompts/<name>.prompt.md`):

- [ ] **What should each prompt ask the agent to do?** Be specific about the scope and expected output format
- [ ] **What inputs does each prompt need?** (subscription ID, resource group name, previous step's output)
- [ ] **Number your prompts sequentially** so they're easy to run in order

> **Built-in help**: When you create or edit a `.prompt.md` file, Copilot automatically loads the [prompt instruction file](../.github/instructions/prompt.instructions.md) with guidelines on frontmatter, structure, inputs, and output definitions.

### Phase 5: Execute Your Workflow

Run your prompts in order and verify each step:

1. **Discover** — Run your discovery prompt. Verify the inventory captures all resources with their configurations
2. **Assess** — Run your assessment prompt. Verify each WAF pillar is rated with specific findings
3. **Report** — Run your reporting prompt. Verify Mermaid diagrams accurately represent the architecture
4. **Migrate** (optional) — Run your migration prompt. Verify the target architecture and cost comparison make sense

### Phase 6: Verify & Iterate

Review the quality of your outputs:

- [ ] Does the inventory capture all deployed resources?
- [ ] Are WAF pillars rated with evidence-based findings?
- [ ] Do Mermaid diagrams accurately reflect the discovered architecture?
- [ ] Are security findings prioritized (P1-P4)?
- [ ] Are CAF naming/tagging violations identified?
- [ ] Are remediation recommendations specific and actionable?

---

## Assessment Criteria

### WAF Pillar Ratings
Rate each pillar: ✅ Compliant, ⚠️ Partial, or ❌ Non-Compliant

| Pillar | What to Check |
|---|---|
| Reliability | Zone redundancy, health probes, backup, failover, auto-scaling |
| Security | Network isolation, public endpoints, NSGs, managed identity, TLS, secrets |
| Cost Optimization | SKU sizing, auto-scaling, reserved instances, orphaned resources |
| Operational Excellence | IaC coverage, monitoring, diagnostics, alerting, tagging |
| Performance Efficiency | SKU appropriateness, caching, connection pooling, regional placement |

### CAF Compliance
- Naming: `<type>-<workload>-<environment>-<region>`
- Tags: environment, workload, owner, costCenter
- Organization: logical resource group structure

### Mermaid Diagram Standards
- `graph TB` for architecture overview
- `graph LR` for network topology
- Green (`#2ea44f`) for compliant, orange (`#d29922`) for partial, red (`#cf222e`) for non-compliant
- Show resource names, types, and SKUs in node labels
- Flag public endpoints with ⚠️

---

## Expected Outputs

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
└── copilot-instructions.md # 👈 Workspace-wide context
.vscode/
└── mcp.json                # 👈 Your MCP server configuration
docs/
├── discovery-inventory.md  # Resource inventory
├── waf-assessment.md       # WAF/CAF assessment
├── discovery-report.md     # Executive report with Mermaid diagrams
└── migration-plan.md       # PaaS migration plan (optional)
```

---

## Stuck? Check the Examples

This repo includes a **complete reference implementation** showing one way to solve this use case:

| What | Where | Purpose |
|---|---|---|
| Example agents | `.github/agents/discoverer.agent.md`, `reviewer.agent.md`, `reporter.agent.md`, `migration-planner.agent.md` | See how discovery agents are defined with tools and handoffs |
| Example prompts | `.github/prompts/disc-1-discover.prompt.md`, etc. | See how discovery prompts are structured |
| Instruction files | `.github/instructions/` | Auto-loaded guidelines for writing agents, prompts, skills, and instructions |
| Example MCP config | `.vscode/mcp.json` | See how Azure and Learn MCP servers are configured |
| Sample discovery output | [docs/sample/discovery-example.md](sample/discovery-example.md) | See what a completed discovery report looks like |

> **These are just examples** — your team should build your own agents, prompts, and workflow. Use the examples for inspiration when you get stuck.

---

## Tips

- **Scan before you assess** — Each step builds on the previous step's output
- **Be specific about scope** — Tell your discovery agent exactly which subscription/resource group to scan
- **Use Learn MCP for accuracy** — Reference official Microsoft documentation for WAF/CAF criteria
- **Ask for specific diagrams** — Network topology, security boundaries, compliance heatmaps
- **Build agents incrementally** — Create one agent, test it, then build the next
- **The migration plan adds depth** — It's optional but demonstrates the full power of the workflow
