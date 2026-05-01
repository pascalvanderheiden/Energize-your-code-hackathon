---
agent: 'planner'
description: 'Create a development plan from the architecture, split into ordered tasks with dependencies for parallel development'
---

# Step 3: Create Development Plan

Create a detailed development plan based on the architecture in `iac/docs/architecture.md` and review findings in `iac/docs/architecture-review.md`.

## Planning Requirements

1. **Break down** the architecture into implementable tasks ordered by dependency layer
2. **Identify parallel work** — which tasks can be implemented simultaneously
3. **Include CI/CD tasks** — GitHub Actions workflows for validation and deployment
4. **Include testing tasks** — Bicep build validation, what-if deployment, security checks
5. **Address review findings** — Incorporate any P1/P2 recommendations from the architecture review

## Expected Output

Create `iac/docs/development-plan.md` containing:
1. Overview with total task count
2. Dependency graph (Mermaid diagram)
3. Tasks grouped by implementation layer (networking → data → compute → security → monitoring → CI/CD → testing)
4. Each task with: ID, title, file path, dependencies, acceptance criteria
5. Parallel execution guide
6. Testing strategy per layer
