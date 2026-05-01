---
agent: 'frontend-dev'
description: 'Build and deploy a sample Todo application to the Azure Web App and verify end-to-end SQL connectivity via Managed Identity'
---

# Step 7.5: Deploy Sample Todo Application

Build a minimal but functional Todo web application, deploy it to the existing Azure Web App, create the SQL database schema, and verify end-to-end connectivity.

## Prerequisites

Before running this step, **Step 7 (Deploy to Azure)** must be complete with:
- Azure Web App running (`app-todo-dev-<region>`)
- Azure SQL Database provisioned with private endpoint
- Managed Identity assigned to the Web App
- `SqlConnection` connection string configured in the Web App

## Skills

Use the **dotnet-ui** skill (`@dotnet-ui`) for .NET UI design patterns and best practices when building the Todo frontend.

## Steps

### 1. Verify Infrastructure

Confirm the deployed infrastructure is ready:
```bash
az webapp show --resource-group <rg-name> --name <webapp-name> --query "state" -o tsv
az sql db show --resource-group <rg-name> --server <sql-server-name> --name <db-name> --query "status" -o tsv
```

### 2. Scaffold the Todo App

Create a .NET 8 ASP.NET Core application under `iac/app/` with:

- **Model**: `TodoItem` with Id, Title, IsComplete, CreatedAt
- **DbContext**: EF Core `TodoDbContext` using the `SqlConnection` connection string (already configured via App Service)
- **API Endpoints**:
  - `GET /api/todos` — list all todos
  - `POST /api/todos` — create a todo
  - `PUT /api/todos/{id}` — toggle completion
  - `DELETE /api/todos/{id}` — delete a todo
- **UI**: A simple, clean HTML page with a form to add todos and a list to display them. Use the **dotnet-ui** skill for design guidance on layout, styling, and accessibility.
- **Health**: `GET /health` — returns 200 OK (matches the Web App health check path)
- **Auto-Migration**: Call `Database.Migrate()` on startup to create the database schema automatically

### 3. Configure for Managed Identity

The app must use Managed Identity for SQL authentication — **no passwords**. Read the connection string from App Service configuration:
```csharp
var connectionString = builder.Configuration.GetConnectionString("SqlConnection");
```

The connection string is already configured in the Web App with `Authentication=Active Directory Managed Identity`.

### 4. Build and Deploy

```bash
cd iac/app
dotnet publish -c Release -o ./publish
cd publish && zip -r ../app.zip .
az webapp deploy --resource-group <rg-name> --name <webapp-name> --src-path ../app.zip --type zip
```

### 5. Grant SQL Permissions

Grant the Managed Identity database roles so it can run migrations and read/write data:
```sql
CREATE USER [<managed-identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<managed-identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<managed-identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<managed-identity-name>];
```

### 6. Verify End-to-End

1. Open the Web App URL in a browser: `https://<webapp-name>.azurewebsites.net`
2. Verify the Todo UI loads
3. Create a new todo item — confirm it appears in the list
4. Mark a todo as complete — confirm the toggle works
5. Delete a todo — confirm it disappears
6. Hit `/health` — confirm it returns 200
7. Hit `/api/todos` — confirm the JSON response matches the UI

## Expected Output

```
iac/app/
├── Program.cs
├── TodoApp.csproj
├── Models/
│   └── TodoItem.cs
├── Data/
│   └── TodoDbContext.cs
└── wwwroot/
    └── (static assets)
```

Plus a **live, working Todo application** on the deployed Azure Web App with data persisted in Azure SQL via Managed Identity.

## Verification Checklist

- [ ] Web App serves the Todo application on HTTPS
- [ ] Can create, read, update, and delete todo items
- [ ] Data is persisted in Azure SQL Database
- [ ] `/health` endpoint returns 200 OK
- [ ] No passwords or secrets in application code
- [ ] Managed Identity authentication is used for SQL
