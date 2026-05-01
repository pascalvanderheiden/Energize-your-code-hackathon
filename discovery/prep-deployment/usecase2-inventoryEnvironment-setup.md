# Use Case 2 Inventory Environment Setup

## Overview
This environment deploys a hub-and-spoke Azure topology for a 3-tier inventory app with private SQL access, centralized monitoring, and traffic validation scripts.

## Topology Summary
- Hub VNet (`10.100.0.0/16`): Azure Firewall + Firewall Policy.
- App Spoke (`10.110.0.0/16`):
  - `ApplicationGatewaySubnet` with Application Gateway WAF_v2.
  - `WebSubnet` with 2 web VMs.
  - `AppSubnet` with Internal Load Balancer and 2 app VMs.
- Management Spoke (`10.120.0.0/16`): Azure Bastion + monitoring private endpoint subnet.
- Data Spoke (`10.130.0.0/16`): SQL private endpoint subnet.

## Core Security and Routing
- UDRs on web/app subnets route traffic through Azure Firewall.
- NSGs enforce tier boundaries:
  - AGW to Web (`TCP/80`)
  - Web to App (`TCP/8080`)
  - Bastion to Web/App (`TCP/3389`)
- Firewall policy allows required east-west and selected outbound flows.

## Platform Services
- Azure SQL Server + `inventorydb` database with public network access disabled.
- User-assigned managed identity (`invuc2-app-sql-uami`) is assigned to app VMs and configured as SQL Entra admin.
- SQL private endpoint and `privatelink.database.windows.net` private DNS integration.
- Application Gateway WAF_v2 as internet ingress.

## Live Database Interaction (Current Design)
- Each app VM uses its assigned managed identity to request a token from IMDS (`169.254.169.254`).
- The app VM extension uses that token to connect to Azure SQL over the private endpoint path.
- The extension seeds/updates `dbo.InventoryItems` and reads a row (`ItemId/ItemName/Qty`).
- The DB value is rendered in `C:\inetpub\app\index.html` (DB line visible via app endpoint).
- One `CustomScriptExtension` per VM is maintained to avoid extension conflicts.

## Monitoring and Private Monitoring Connectivity
- Log Analytics Workspace.
- Data Collection Endpoint (DCE).
- Data Collection Rule (DCR) for VM performance counters and Windows event streaming.
  - Streams: `Microsoft-Perf` and `Microsoft-Event`.
  - Explicit `dependsOn` enforces DCR creation after AMA extension completion on `webvm0`, `webvm1`, `appvm0`, and `appvm1`.
- Azure Monitor Agent extension on all web/app VMs.
- DCR associations on all web/app VMs.
- Azure Monitor Private Link Scope (AMPLS) + private endpoint in management spoke.
- Private DNS zones for monitor endpoints:
  - `privatelink.monitor.azure.com`
  - `privatelink.oms.opinsights.azure.com`
  - `privatelink.ods.opinsights.azure.com`
  - `privatelink.agentsvc.azure-automation.net`
- Diagnostic settings route platform diagnostics to Log Analytics (resource dependent).

## Network Flow Logs and Traffic Analytics
- NSG Flow Logs support is optional in this template:
  - Controlled by parameter `enableNsgFlowLogs` (default: `false`).
  - Use `true` only in environments where NSG flow log creation is allowed.
- When enabled, NSG Flow Logs are configured for:
  - `invuc2-web-nsg`
  - `invuc2-app-nsg`
- Flow logs are written to a dedicated storage account (`networkWatcherFlowLogsStorageName`).
- Traffic Analytics sends enriched connectivity insights to the same Log Analytics workspace.
- The template targets an existing Network Watcher in `NetworkWatcherRG` (regional watcher pattern: `NetworkWatcher_<region>`).
- Added parameters:
  - `networkWatcherResourceGroup` (default: `NetworkWatcherRG`)
  - `networkWatcherName` (default: `NetworkWatcher_<location>`)
  - `enableNsgFlowLogs` (default: `false`)

## SQL Security
- SQL security alert policy (`Default`) enabled.

## Tagging Strategy
- Common tags are defined in `variables.commonTags` following CAF lowercase conventions:
  - `usecase=agentic-discovery`
  - `existinginfra=yes`
  - `environment=dev`
  - `workload=agentic-discovery`
  - `owner=tbd-owner`
  - `costcenter=tbd-cost-center`
- Tags are applied to all major tag-capable resources in the template.
- Resource group tags are applied during deployment using `Microsoft.Resources/tags` with resource name `default`.

## Files
- Template: `usecase2-inventoryEnvironment.json`
- Parameters: `usecase2-inventoryEnvironment.parameters.json`
- Architecture diagram: `usecase2-inventoryEnvironment.mmd`

## Deploy to Azure Button
Host the template file at a public HTTPS URL (for example, a raw GitHub URL), then replace `<ENCODED_TEMPLATE_URI>` below with the URL-encoded template URI.

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/<ENCODED_TEMPLATE_URI>)

Example encoded template URI format:
- `https%3A%2F%2Fraw.githubusercontent.com%2F<org>%2F<repo>%2F<branch>%2Fusecase2-inventoryEnvironment.json`

## Prerequisites
- Azure CLI logged in.
- Target subscription selected.
- Resource group created (example: `rg-usecase2-inventory`).
- Parameter values provided for:
  - `adminPassword` (pass securely at deploy time or via Key Vault reference; do not store in the parameters file)

## Deploy
```powershell
$tpl = "<REPO_ROOT>/discovery/prep-deployment/usecase2-inventoryEnvironment.json"
$par = "<REPO_ROOT>/discovery/prep-deployment/usecase2-inventoryEnvironment.parameters.json"
az account set --subscription <YOUR_SUBSCRIPTION_ID>
az deployment group create \
  --resource-group rg-usecase2-agenticdiscovery \
  --template-file $tpl \
  --parameters @$par \
  --parameters adminPassword='<YOUR_ADMIN_PASSWORD>'
```

> **Note:** If `enableNsgFlowLogs` is set to `true`, the template targets the `NetworkWatcherRG` resource group via a nested cross-resource-group deployment. This requires subscription-scope deployment (`az deployment sub create`) and appropriate permissions. Leave `enableNsgFlowLogs` at its default value of `false` for resource-group scope deployment.

## Validation Checks
- Confirm AGW front end serves web content over **HTTPS** with a valid TLS certificate (configure an HTTPS listener with a certificate; HTTP should redirect to HTTPS).
- Confirm app tier responds through ILB (`10.110.2.100:8080`).
- Confirm app tier can reach SQL over private endpoint (`1433`).
- Confirm app tier page shows DB row values (for example `ItemId=1001;Name=Widget;Qty=42`).
- Confirm Bastion RDP to web/app VMs.
- Check VM script outputs:
  - `C:\validation\web-flow-results.txt`
  - `C:\validation\app-flow-results.txt`
- Check diagnostics and DCR associations in Azure Monitor.
- In Log Analytics, verify Traffic Analytics data appears for NSG flow logs.
- Confirm both flow-log resources exist under the regional Network Watcher:
  - `web-nsg-flowlogs`
  - `app-nsg-flowlogs`

## Optional: Defender Plans (Subscription Scope)
Defender pricing resources are subscription-scope objects and should be applied outside this resource-group template.

```powershell
az account set --subscription <YOUR_SUBSCRIPTION_ID>
az resource create --id "/subscriptions/<YOUR_SUBSCRIPTION_ID>/providers/Microsoft.Security/pricings/VirtualMachines" --api-version 2024-01-01 --properties '{"pricingTier":"Standard"}'
az resource create --id "/subscriptions/<YOUR_SUBSCRIPTION_ID>/providers/Microsoft.Security/pricings/SqlServers" --api-version 2024-01-01 --properties '{"pricingTier":"Standard"}'
```
