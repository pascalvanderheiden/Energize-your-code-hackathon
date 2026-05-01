---
agent: 'architect'
description: 'Design a greenfield Azure infrastructure architecture with Mermaid diagrams, WAF/CAF alignment, and AVM module selection'
---

# Step 1: Design Architecture

Create a greenfield infrastructure architecture for the following Azure workload:

## Requirements

- **Azure Web App** (App Service) hosting a sample Todo application (.NET or Node.js)
- **Azure SQL Database** for storing todo items persistently
- **Privately connected** — the database must NOT be publicly accessible, use Private Endpoints
- **NSG implemented** with explicit allow/deny traffic rules following least-privilege
- **Azure Virtual Network** with proper subnet design for network isolation

## Constraints

- Use **Azure Verified Modules (AVM)** from the official Bicep public registry for all resources
- Follow **CAF naming conventions** (e.g., `app-todo-dev-norwayeast`, `sql-todo-dev-norwayeast`)
- Apply **WAF principles** across all 5 pillars (Reliability, Security, Cost, Operations, Performance)
- Target region: **Norway East** (or parameterized)
- Target environment: **dev** (with prod considerations documented)
- Use **Managed Identity** for App-to-DB authentication (no passwords in code)

## Expected Output

Create `iac/docs/architecture.md` containing:
1. Executive summary
2. Architecture diagram (Mermaid)
3. Network topology diagram (Mermaid)
4. Resource inventory table with CAF names and AVM module references
5. WAF pillar analysis table
6. Security design details
7. Deployment approach
