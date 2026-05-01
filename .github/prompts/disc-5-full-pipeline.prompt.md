---
agent: 'discoverer'
description: 'Run the complete discovery pipeline: discover, assess, report in one shot'
---

# Full Discovery Pipeline

Run the complete discovery, assessment, and reporting pipeline for an Azure environment.

## Target Environment

- **Subscription**: (specify subscription name or ID)
- **Resource Group(s)**: (specify resource group name(s) or "all")

## Pipeline Steps

Execute these steps sequentially:

### Step 1: Discover (@discoverer)
Scan the Azure environment using Azure MCP and produce `discovery/docs/discovery-inventory.md` with the complete resource inventory.

### Step 2: Assess (@reviewer)
Assess the inventory against WAF pillars and CAF conventions using Learn MCP. Produce `discovery/docs/waf-assessment.md` with compliance ratings and gap analysis.

### Step 3: Report (@reporter)
Synthesize inventory and assessment into `discovery/docs/discovery-report.md` with executive summary and Mermaid architecture diagrams.

### Step 4: Migration (Optional) (@migration-planner)
If the discovered environment contains IaaS resources, create `discovery/docs/migration-plan.md` with PaaS migration roadmap and cost assessment.

## Expected Outputs

| Document | Agent | Content |
|---|---|---|
| `discovery/docs/discovery-inventory.md` | @discoverer | Resource inventory, network topology, security snapshot |
| `discovery/docs/waf-assessment.md` | @reviewer | WAF pillar ratings, CAF audit, prioritized findings |
| `discovery/docs/discovery-report.md` | @reporter | Executive report with Mermaid diagrams |
| `discovery/docs/migration-plan.md` | @migration-planner | PaaS migration plan with cost assessment (optional) |
