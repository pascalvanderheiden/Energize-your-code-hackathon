---
agent: 'discoverer'
description: 'Discover and inventory all Azure resources in a subscription or resource group using the Azure MCP server'
---

# Step 1: Discover Azure Environment

Connect to the Azure environment and discover all deployed resources.

## Target Environment

- **Subscription**: (specify subscription name or ID)
- **Resource Group(s)**: (specify resource group name(s) or "all")
- **Azure Region**: (if filtering by region, specify here)

## Discovery Scope

Scan and inventory the complete environment including:

1. **Resource Groups** — names, locations, tags, resource counts
2. **Compute** — VMs, App Services, Container Apps, Function Apps
3. **Networking** — VNets, subnets, NSGs, public IPs, private endpoints, load balancers
4. **Data & Storage** — SQL Servers/DBs, Storage Accounts, Cosmos DB, Redis
5. **Security & Identity** — Key Vaults, managed identities, role assignments
6. **Monitoring** — Log Analytics, Application Insights, diagnostic settings, alerts

## Expected Output

Create `discovery/docs/discovery-inventory.md` containing:
1. Discovery summary with subscription details and scan timestamp
2. Resource group overview table
3. Detailed resource inventory (per resource group) with name, type, SKU, location, tags, key configs
4. Network topology summary
5. Security snapshot (public endpoints, identity usage, Key Vault presence)

## Next Step

After discovery is complete, hand off to `@reviewer` for WAF/CAF compliance analysis.
