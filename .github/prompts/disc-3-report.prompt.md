---
agent: 'reporter'
description: 'Synthesize discovery and assessment into an executive report with Mermaid architecture diagrams'
---

# Step 3: Generate Discovery Report

Synthesize the discovery inventory and WAF assessment into a polished executive report with rich Mermaid architecture diagrams.

## Input

- Read `discovery/docs/discovery-inventory.md` — resource inventory
- Read `discovery/docs/waf-assessment.md` — WAF/CAF assessment

## Report Requirements

### Mermaid Diagrams (minimum 2, ideally 4)
1. **Architecture Overview** (`graph TB`) — all resources grouped by RG, VNet, subnet with compliance color-coding
2. **Network Topology** (`graph LR`) — VNets, subnets, NSGs, public IPs, private endpoints, peerings
3. **Security Boundaries** (`graph TB`) — trust zones, data flows, identity connections
4. **Data Flow** (`graph LR`) — user → app → data → monitoring paths

### Compliance Color-Coding
- ✅ Compliant: `fill:#2ea44f,color:#fff` (green)
- ⚠️ Partial: `fill:#d29922,color:#fff` (orange)
- ❌ Non-Compliant: `fill:#cf222e,color:#fff` (red)

### Report Sections
1. Executive summary (3-5 sentences for non-technical stakeholders)
2. Architecture diagrams with explanations
3. Environment profile (resource counts, SKU distribution)
4. WAF compliance dashboard
5. CAF compliance summary
6. Risk register with severity ratings
7. Top 10 actionable recommendations with effort estimates

## Expected Output

Create `discovery/docs/discovery-report.md` with the complete executive report and embedded Mermaid diagrams.

## Next Step (Optional)

If the user wants a migration plan, hand off to `@migration-planner` for PaaS migration planning.
