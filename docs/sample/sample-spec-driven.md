# Sample Walkthrough: Kanban Board with Drag-and-Drop

> A complete step-by-step walkthrough of Use Case 3 — building a Kanban task board with drag-and-drop using spec-driven development. All commands and prompts are ready to copy/paste.

---

## What You'll Build

A **Kanban-style task board** with:
- Three columns: **Backlog**, **In Progress**, **Done**
- Drag-and-drop tasks between columns
- Create, edit, and delete tasks
- Task priority labels (Low, Medium, High)
- Persistent storage via local storage
- Responsive design for desktop and mobile

**Tech stack**: React with TypeScript, using `@dnd-kit` for drag-and-drop.

---

## Prerequisites

Before starting, ensure you have the following installed:

### Node.js 20+

```bash
# macOS (Homebrew)
brew install node

# Windows (winget)
winget install OpenJS.NodeJS.LTS

# Linux (nvm)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
nvm install 20
```

Verify: `node --version` (should show v20+)

### Python 3.12+ with uv (Spec Kit only)

```bash
# macOS (Homebrew)
brew install python@3.12

# Then install uv (macOS / Linux)
curl -LsSf https://astral.sh/uv/install.sh | sh
```

```powershell
# Windows (winget + PowerShell)
winget install Python.Python.3.12
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Verify: `python3 --version` (macOS/Linux) or `python --version` (Windows) and `uv --version`

### Azure CLI (for deployment)

```bash
# macOS (Homebrew)
brew install azure-cli

# Windows (winget)
# winget install Microsoft.AzureCLI
```

Log in:
```bash
az login
az account show --query "{name:name, id:id, tenantId:tenantId}" -o table
```

> Confirm the displayed subscription and tenant with the hackathon coaches before deploying.

---

## Step 0: Create a New Project Folder

**macOS / Linux**:
```bash
mkdir ~/kanban-board && cd ~/kanban-board
code .
```

**Windows (PowerShell)**:
```powershell
mkdir $HOME\kanban-board; cd $HOME\kanban-board
code .
```

All remaining steps happen in this new VS Code window.

---

## Step 1: Choose Your Tool & Initialize

Pick **one** option and follow its track through the entire walkthrough.

### Option A: Spec Kit

**Terminal**:
```bash
specify init --here --ai copilot
```

### Option B: OpenSpec

**Terminal**:
```bash
npm install -g @fission-ai/openspec@latest
openspec init
```

---

## Step 2: Brainstorm — Define the Kanban Board

Open **Copilot Chat - Ask Mode** to refine and brainstorm on your idea, the more info you are able to produce in later stages, the better. This could be example output of your brainstorm and refinement.

```
I'm starting a new web application project using spec-driven development.
Help me define the app. I need:
1. A clear app name and one-liner description
2. User stories with acceptance criteria
3. A data model
4. Recommended tech stack

App idea: A Kanban-style task management board with drag-and-drop functionality.

Here are my requirements:
- App name: KanbanFlow
- Description: A visual task management board with drag-and-drop columns
- Tech stack: React with TypeScript, Vite for bundling, @dnd-kit for drag-and-drop, local storage for persistence

User Stories:

US-1: View the board
As a user, I want to see a board with three columns (Backlog, In Progress, Done)
so that I can see the status of all my tasks at a glance.
- AC: Board displays three columns with headers
- AC: Each column shows the count of tasks it contains
- AC: Empty columns show a "No tasks" placeholder

US-2: Create a task
As a user, I want to create a new task with a title and priority
so that I can add work items to my board.
- AC: "Add Task" button opens a form/modal
- AC: Form has fields for title (required) and priority (Low/Medium/High, default Medium)
- AC: Submitting creates the task in the Backlog column
- AC: Empty title is rejected with an error message
- AC: Title max length is 200 characters

US-3: Drag and drop tasks
As a user, I want to drag tasks between columns
so that I can update their status visually.
- AC: Tasks can be picked up by clicking and holding
- AC: Visual feedback shows where the task will be dropped
- AC: Tasks can be moved between any two columns
- AC: Task order within a column is preserved after drop
- AC: Changes persist after page refresh

US-4: Edit a task
As a user, I want to edit a task's title and priority
so that I can correct or update task details.
- AC: Clicking a task opens an edit form/modal
- AC: Title and priority can be changed
- AC: Saving updates the task in place
- AC: Cancel discards changes

US-5: Delete a task
As a user, I want to delete a task
so that I can remove items that are no longer relevant.
- AC: Each task has a delete button/icon
- AC: Clicking delete removes the task from the board
- AC: Deletion persists after page refresh

Data Model:
- Task: { id: string, title: string, priority: "low" | "medium" | "high", column: "backlog" | "in-progress" | "done", order: number, createdAt: string }
- Columns are fixed: Backlog, In Progress, Done
- Persistence: local storage (key: "kanbanflow-tasks")
```

**Verify before continuing**:
- [ ] Copilot confirmed the 5 user stories
- [ ] Data model looks correct
- [ ] Scope feels achievable

---

## Step 3: Write Specifications

### If using Spec Kit

**Copilot Chat** — set the constitution:
```
/speckit.constitution
Project: KanbanFlow
Goal: A visual Kanban task management board with drag-and-drop between Backlog, In Progress, and Done columns
Tech stack: React with TypeScript, Vite, @dnd-kit/core and @dnd-kit/sortable for drag-and-drop, local storage for persistence
Constraints: No backend, local storage only, responsive design, must work in modern browsers
```

**Copilot Chat** — write specs:
```
/speckit.specify
Write specifications for the KanbanFlow app with these features:

Feature 1 — Board Layout:
Three columns (Backlog, In Progress, Done) each showing task count and a "No tasks" placeholder when empty.

Feature 2 — Create Task:
"Add Task" button that opens a form with title (required, max 200 chars) and priority (Low/Medium/High, default Medium). New tasks go to Backlog.

Feature 3 — Drag and Drop:
Tasks can be dragged between any two columns. Visual drag feedback, order preserved within columns, changes persist in local storage.

Feature 4 — Edit Task:
Click a task to open edit form. Change title and priority. Save or cancel.

Feature 5 — Delete Task:
Delete button on each task. Removes from board and local storage.

Describe WHAT to build, not HOW. Include acceptance criteria and edge cases for each feature.
```

### If using OpenSpec

**Copilot Chat** — propose each feature:
```
/opsx:propose "Board Layout: Three-column Kanban board (Backlog, In Progress, Done). Each column shows a header with task count. Empty columns display 'No tasks' placeholder. Responsive layout that works on desktop and mobile."
```

```
/opsx:propose "Create Task: 'Add Task' button opens a form with title field (required, max 200 chars) and priority dropdown (Low/Medium/High, default Medium). Submitting adds the task to the Backlog column. Empty titles show an error. Form clears after submit."
```

```
/opsx:propose "Drag and Drop: Tasks can be dragged between any two columns. Visual feedback during drag shows drop target. Task order within columns is preserved. All changes persist to local storage and survive page refresh."
```

```
/opsx:propose "Edit Task: Clicking a task opens an edit form showing current title and priority. User can change values and save, or cancel to discard. Updated task stays in same column and position."
```

```
/opsx:propose "Delete Task: Each task card has a delete button/icon. Clicking it removes the task from the board and from local storage. Deletion is immediate (no confirmation required for MVP)."
```

**Verify before continuing**:
- [ ] Specs cover all 5 features
- [ ] Each spec has acceptance criteria
- [ ] Edge cases are included (empty title, max length, persistence)

---

## Step 4: Generate the Implementation

### If using Spec Kit

**Copilot Chat**:
```
/speckit.plan
```

Review the generated plan. Then:
```
/speckit.tasks
```

Review the task list. Then:
```
/speckit.implement in this directory and do NOT DELETE excisting files from this folder
```

### If using OpenSpec

**Copilot Chat**:
```
/opsx:apply
```

### Verify the app locally

**Terminal**:
```bash
npm install
npm run dev
```

Open `http://localhost:5173` (Vite default) in your browser.

**Manual test checklist**:
- [ ] Three columns visible with headers
- [ ] Can create a task with title and priority
- [ ] Task appears in Backlog column
- [ ] Can drag a task from Backlog to In Progress
- [ ] Can drag a task from In Progress to Done
- [ ] Refresh page — tasks are still there
- [ ] Can edit a task's title
- [ ] Can delete a task
- [ ] Empty title is rejected

---

## Step 5: Write and Run E2E Tests

**Terminal** — initialize Playwright:
```bash
npm init playwright@latest
npx playwright install --with-deps msedge
```

**Copilot Chat** — generate tests:
```
Write Playwright E2E tests for my KanbanFlow app. The app runs at http://localhost:5173.
Create tests in the tests/ folder. Use data-testid attributes for selectors.

Test the following user stories:

US-1 Board Layout:
- Board shows three columns: Backlog, In Progress, Done
- Each column shows a task count
- Empty columns show a "No tasks" placeholder

US-2 Create Task:
- User can open the add task form
- User can create a task with title "Buy groceries" and priority "High"
- Task appears in the Backlog column
- Empty title is rejected with an error
- Title longer than 200 characters is handled

US-3 Drag and Drop:
- User can drag a task from Backlog to In Progress
- User can drag a task from In Progress to Done
- Task order is preserved after drag
- Changes persist after page refresh

US-4 Edit Task:
- User can click a task to edit it
- User can change the title and save
- User can cancel editing without changes

US-5 Delete Task:
- User can delete a task
- Deleted task is no longer visible
- Deletion persists after refresh

Use test.describe blocks per user story. Add test.beforeEach to navigate to the app.
Clear local storage between tests for isolation.
```

**Terminal** — run tests:
```bash
npx playwright test
```

If tests fail, paste the error output into **Copilot Chat**:
```
My Playwright tests are failing with these errors:
<PASTE ERROR OUTPUT HERE>
Fix the tests or the application code to make them pass.
```

**Terminal** — run again and view report:
```bash
npx playwright test
npx playwright show-report
```

**Verify before continuing**:
- [ ] All tests pass
- [ ] Each user story has at least one test
- [ ] Tests cover happy path, validation, and edge cases

---

## Step 6: Deploy to Azure

**Copilot Chat**:
```
Deploy my KanbanFlow React app to Azure Static Web Apps.
The app uses Vite and the build output is in the dist/ folder.
Use resource group: rg-kanbanflow-dev-westeurope
Use location: westeurope
Give me the az CLI commands to:
1. Create a resource group
2. Build the app
3. Deploy to Static Web Apps
```

**macOS / Linux**:
```bash
npm run build

az group create --name rg-kanbanflow-dev-westeurope --location westeurope

az staticwebapp create \
  --name kanbanflow \
  --resource-group rg-kanbanflow-dev-westeurope \
  --source ./dist \
  --location westeurope
```

**Windows (PowerShell)**:
```powershell
npm run build

az group create --name rg-kanbanflow-dev-westeurope --location westeurope

az staticwebapp create `
  --name kanbanflow `
  --resource-group rg-kanbanflow-dev-westeurope `
  --source ./dist `
  --location westeurope
```

**Post-deployment verification**:
- [ ] App is accessible at the Azure URL
- [ ] All three columns display correctly
- [ ] Drag-and-drop works on the deployed version
- [ ] Run tests against the deployed app:
  ```bash
  # macOS / Linux
  BASE_URL=https://kanbanflow.azurestaticapps.net npx playwright test

  # Windows (PowerShell)
  $env:BASE_URL="https://kanbanflow.azurestaticapps.net"; npx playwright test
  ```

---

## Feature Expansion: Add Task Due Dates

After the MVP, add a new feature using the same cycle:

### If using Spec Kit

**macOS / Linux**:
```bash
export SPECIFY_FEATURE="due-dates"
```

**Windows (PowerShell)**:
```powershell
$env:SPECIFY_FEATURE="due-dates"
```

**Copilot Chat**:
```
/speckit.specify
Write a spec for adding due dates to tasks in KanbanFlow:
- Each task has an optional due date field
- Due date is shown on the task card
- Overdue tasks are highlighted in red
- Tasks can be sorted by due date within a column
```

Then: `/speckit.implement` → write tests → deploy.

### If using OpenSpec

**Copilot Chat**:
```
/opsx:propose "Due Dates: Add an optional due date field to tasks. Due date is displayed on the task card. Overdue tasks (past due date) are highlighted with a red border. Tasks within a column can be sorted by due date."
/opsx:apply
```

Then: write tests → deploy.

---

## Expected Final Output

```
kanban-board/
├── src/
│   ├── components/
│   │   ├── Board.tsx          # Main board with three columns
│   │   ├── Column.tsx         # Single column component
│   │   ├── TaskCard.tsx       # Draggable task card
│   │   ├── TaskForm.tsx       # Create/edit task form
│   │   └── ...
│   ├── hooks/
│   │   └── useLocalStorage.ts # Local storage persistence hook
│   ├── types/
│   │   └── index.ts           # Task and Column types
│   ├── App.tsx
│   └── main.tsx
├── tests/
│   ├── board-layout.spec.ts
│   ├── create-task.spec.ts
│   ├── drag-and-drop.spec.ts
│   ├── edit-task.spec.ts
│   └── delete-task.spec.ts
├── specs/                     # Specifications (Spec Kit)
│   └── ...
├── openspec/                  # Specifications (OpenSpec — if chosen)
│   └── changes/
├── playwright.config.ts
├── package.json
├── vite.config.ts
└── tsconfig.json
```

A deployed Kanban board on Azure accessible via HTTPS, with all Playwright tests passing.
