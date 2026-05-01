# Use Case 3: Spec-Driven Development — Greenfield Application

Build a greenfield web application using specification-first methodology with AI-powered implementation.

## Objective

Use a **spec-driven workflow** to brainstorm, specify, implement, test, and deploy a web application. Your team decides WHAT to build, chooses between **Spec Kit** or **OpenSpec** as spec-driven tooling, and follows this manual step by step. All work happens in a **separate empty folder** outside this repo.

## Prerequisites

### Node.js 20+ (for OpenSpec and Playwright)

Download from [https://nodejs.org](https://nodejs.org) or install via a package manager:

```bash
# macOS (Homebrew)
brew install node

# Windows (winget)
winget install OpenJS.NodeJS.LTS

# Linux (nvm — recommended)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
nvm install 20
```

Verify: `node --version` (should show v20+)

### Python 3.12+ with uv (for Spec Kit only)

Install Python from [https://www.python.org/downloads/](https://www.python.org/downloads/) or via a package manager:

```bash
# macOS (Homebrew)
brew install python@3.12

# Windows (winget)
winget install Python.Python.3.12
```

Then install **uv** (fast Python package manager):

```bash
# macOS / Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# Windows (PowerShell)
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Verify: `python3 --version` (should show 3.12+) and `uv --version`

> **uv docs**: [https://docs.astral.sh/uv/](https://docs.astral.sh/uv/)

### VS Code with GitHub Copilot

- Download VS Code: [https://code.visualstudio.com](https://code.visualstudio.com)
- Install the **GitHub Copilot** extension from the [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)
- Or use **GitHub Codespaces** (no local install needed)

### Playwright (for E2E testing)

To run Playwright **E2E test suites** from the terminal, install the test runner and its browsers in your project folder (done in Step 5).

### Azure CLI (Required for Deployment)

Install the Azure CLI from [https://learn.microsoft.com/cli/azure/install-azure-cli](https://learn.microsoft.com/cli/azure/install-azure-cli) or:

```bash
# macOS (Homebrew)
brew install azure-cli

# Windows (winget)
winget install Microsoft.AzureCLI

# Linux (script)
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

If you plan to deploy your app to Azure, log in first using the credentials provided by the hackathon coaches:

```bash
az login
```

Verify you are on the correct subscription:

```bash
az account show --query "{name:name, id:id, tenantId:tenantId}" -o table
```

> **Important**: Confirm the displayed subscription and tenant with the hackathon coaches before deploying.

---

## Step 0: Create a New Project Folder

All work for this use case happens in a **separate empty folder** — not inside this hackathon repo.

**macOS / Linux**:
```bash
mkdir ~/my-spec-app && cd ~/my-spec-app
code .
```

**Windows (PowerShell)**:
```powershell
mkdir $HOME\my-spec-app; cd $HOME\my-spec-app
code .
```

This opens a fresh VS Code window. All remaining steps happen in this new workspace.

---

## Step 1: Choose Your Spec-Driven Tool

Pick **one** of the two options below. Both follow the same overall workflow but use different commands.

### Option A: Spec Kit (Python-based)

**Terminal**:
```bash
specify init --here --ai copilot
```

This creates a `specs/` folder and registers Spec Kit commands in Copilot Chat. You'll use these slash commands later:

| Command | Purpose |
|---|---|
| `/speckit.constitution` | Set project constitution (goals, constraints) |
| `/speckit.specify` | Write feature specifications |
| `/speckit.plan` | Generate an implementation plan from specs |
| `/speckit.tasks` | List implementation tasks |
| `/speckit.implement` | Generate code from specs |

### Option B: OpenSpec (Node.js-based)

**Terminal**:
```bash
npm install -g @fission-ai/openspec@latest
openspec init
```

This creates an `openspec/` folder and registers OpenSpec commands in Copilot Chat. You'll use these slash commands later:

| Command | Purpose |
|---|---|
| `/opsx:propose <idea>` | Propose a new feature |
| `/opsx:apply` | Implement the current proposal |
| `/opsx:archive` | Archive completed changes |

**Verify**: Confirm your chosen tool initialized successfully (config files present in the project folder).

---

## Step 2: Brainstorm Your App Idea

Open **Copilot Chat - Ask Mode** to refine and brainstorm on your idea, the more info you are able to produce in later stages, the better. 

**Copilot Chat**:
```
I'm starting a new web application project using spec-driven development.
Help me define the app. I need:
1. A clear app name and one-liner description
2. 3-5 user stories in "As a [role], I want [action] so that [benefit]" format
3. Acceptance criteria for each user story
4. A basic data model (entities and relationships)
5. Recommended tech stack (keep it simple — e.g., React, Vue, or plain HTML/JS)

My app idea: <DESCRIBE YOUR IDEA HERE>
```

Replace `<DESCRIBE YOUR IDEA HERE>` with your team's idea — for example:
- A Kanban-style task board with drag-and-drop
- An expense tracker with categories and charts
- A recipe book with search and ratings
- A habit tracker with streaks

**Verify before continuing**:
- [ ] App idea is defined with a clear purpose
- [ ] 3-5 user stories with acceptance criteria
- [ ] Data model covers all features
- [ ] Scope is realistic for a hackathon (not too ambitious)

---

## Step 3: Write Your Specifications

Use your chosen tool's commands to capture what you brainstormed as formal specs.

### If using Spec Kit

**Copilot Chat** — set the project constitution first:
```
/speckit.constitution
Project: <YOUR APP NAME>
Goal: <ONE-LINE DESCRIPTION>
Tech stack: <e.g., React with TypeScript>
Constraints: Keep it simple, local storage for persistence, no backend required for MVP
```

**Copilot Chat** — then write specs for each feature:
```
/speckit.specify
Write specifications for the following features:
1. <Feature 1 from your user stories>
2. <Feature 2 from your user stories>
3. <Feature 3 from your user stories>
Each spec should include: description, acceptance criteria, data model, and edge cases.
Describe WHAT to build, not HOW.
```

### If using OpenSpec

**Copilot Chat** — propose each feature:
```
/opsx:propose "<Feature 1>: <brief description with acceptance criteria>"
```

Repeat for each feature:
```
/opsx:propose "<Feature 2>: <brief description with acceptance criteria>"
```

**Key principle**: Specify **WHAT** to build, not **HOW**. Focus on behavior, not implementation details.

**Verify before continuing**:
- [ ] Specs exist for all 3-5 core features
- [ ] Each spec has clear acceptance criteria
- [ ] Specs describe WHAT, not HOW (no implementation details)
- [ ] Edge cases are covered (empty input, max length, etc.)

---

## Step 4: Generate the Implementation

### If using Spec Kit

**Copilot Chat**:
```
/speckit.plan
```
Review the plan, then:
```
/speckit.tasks
```
Review the tasks, then:
```
/speckit.implement in this directory and do NOT DELETE excisting files from this folder.
```

### If using OpenSpec

**Copilot Chat**:
```
/opsx:apply
```

**Start and verify locally**:

**Terminal**:
```bash
npm install
npm run dev
```

Open `http://localhost:3000` (or whichever port is shown) and manually test each user story.

**Verify before continuing**:
- [ ] App starts without errors
- [ ] Each user story's acceptance criteria work when tested manually
- [ ] Data model matches what was specified
- [ ] UI is usable (not just functional)

---

## Step 5: Write and Run E2E Tests

**Terminal** — initialize Playwright in your project:
```bash
npm init playwright@latest
npx playwright install --with-deps msedge
```

**Copilot Chat** — ask Copilot to generate tests for your user stories:
```
Write Playwright E2E tests for my application. Create one test file per feature.
My user stories and acceptance criteria are:

1. <User Story 1 + acceptance criteria>
2. <User Story 2 + acceptance criteria>
3. <User Story 3 + acceptance criteria>

Put tests in the tests/ folder. Use data-testid attributes for selectors.
Make sure to test happy paths, validation, and edge cases.
```

**Terminal** — run the tests:
```bash
npx playwright test              # Run all tests headless
npx playwright test --ui         # Visual test runner
npx playwright show-report       # View HTML report
```

**Verify before continuing**:
- [ ] Test files exist in `tests/` directory
- [ ] All tests pass (`npx playwright test` shows green)
- [ ] Each user story has at least one test
- [ ] Edge cases are tested (empty input, long text)

---

## Step 6: Deploy to Azure

**Copilot Chat** — ask Copilot to help you deploy:
```
Deploy my web application to Azure. My app is a <SPA / full-stack app / static site>.
Help me choose the best Azure service (Static Web Apps, App Service, or Container Apps)
and give me the az CLI commands to deploy.
Use resource group name: rg-<myapp>-dev-westeurope
Use location: westeurope
```

**Example deployment (Static Web Apps)**:

**macOS / Linux**:
```bash
npm run build
az staticwebapp create \
  --name <myapp> \
  --resource-group rg-<myapp>-dev-westeurope \
  --source ./dist \
  --location westeurope
```

**Windows (PowerShell)**:
```powershell
npm run build
az staticwebapp create `
  --name <myapp> `
  --resource-group rg-<myapp>-dev-westeurope `
  --source ./dist `
  --location westeurope
```

**Post-deployment verification**:
- [ ] App is accessible at the Azure URL
- [ ] All features work on the deployed version
- [ ] Run Playwright tests against the deployed URL:
  ```bash
  # macOS / Linux
  BASE_URL=https://your-app.azurestaticapps.net npx playwright test

  # Windows (PowerShell)
  $env:BASE_URL="https://your-app.azurestaticapps.net"; npx playwright test
  ```

---

## Feature Expansion

After the MVP is deployed, add new features using the same specify → implement → test cycle:

### Spec Kit

**macOS / Linux**:
```bash
export SPECIFY_FEATURE="new-feature-name"
```

**Windows (PowerShell)**:
```powershell
$env:SPECIFY_FEATURE="new-feature-name"
```
**Copilot Chat**:
```
/speckit.specify
Write a spec for: <describe the new feature with acceptance criteria>
```
Then: `/speckit.implement` → write tests → deploy.

### OpenSpec
**Copilot Chat**:
```
/opsx:propose "<New feature description with acceptance criteria>"
/opsx:apply
```
Then: write tests → deploy.

Each feature follows the same cycle: **specify → implement → test → deploy**

---

## Expected File Structure

After completing all steps, your project folder should look like:

```
my-spec-app/
├── src/                        # Application source code
│   ├── components/             # UI components
│   └── ...
├── tests/                      # Playwright E2E tests
│   ├── feature1.spec.ts
│   └── ...
├── specs/                      # Specifications (Spec Kit)
│   └── ...
├── openspec/                   # Specifications (OpenSpec — if chosen)
│   └── changes/
├── playwright.config.ts        # Playwright configuration
├── package.json
└── ...
```

---

## Tips

- **Start small** — Focus on 3-5 core features for the MVP
- **Specs over code** — Spend more time writing good specs than fixing generated code
- **Test everything** — Write Playwright tests before adding new features
- **Local first** — Get it working locally before deploying to Azure
- **Iterate** — It's better to have 3 polished features than 10 broken ones
- **Use Copilot Chat freely** — Paste any prompt or question directly into the chat window
- **Check the sample** — See [docs/sample/sample-spec-driven.md](sample/sample-spec-driven.md) for a complete Kanban board walkthrough with all commands pre-filled
