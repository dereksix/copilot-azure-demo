# Copilot Demo - Tested Commands & Prompts
## All commands verified working as of January 13, 2026

---

## BEFORE THE DEMO

### Generate Issues to Troubleshoot
```powershell
# Run this 5 minutes before demo to populate data:
.\simulate-issues.ps1 -Issue all-issues

# Or generate traffic manually:
.\generate-load.ps1 -Scenario all
```

---

## DEMO SECTION 1: ENVIRONMENT DISCOVERY
**What it shows:** Full visibility into Azure infrastructure

### ✅ TESTED COMMANDS:

**List all resources:**
```powershell
az resource list --resource-group rg-copilot-demo --query "[].{Name:name, Type:type, Location:location}" -o table
```
*Expected output: Shows 10 resources including web apps, SQL, storage, App Insights*

**Check web app status:**
```powershell
az webapp list --resource-group rg-copilot-demo --query "[].{Name:name, State:state, URL:defaultHostName}" -o table
```
*Expected output: Both apps showing "Running" state*

**Check SQL server status:**
```powershell
az sql server show --name sql-copilot-demo-89609 --resource-group rg-copilot-demo --query "{Name:name, FQDN:fullyQualifiedDomainName, State:state}" -o table
```
*Expected output: State = Ready*

### COPILOT PROMPTS:
- "Show me all resources in my Azure resource group rg-copilot-demo"
- "Check if my Azure App Service web apps are running in rg-copilot-demo"
- "Is my Azure SQL database online in rg-copilot-demo?"

---

## DEMO SECTION 2: FRONTEND TROUBLESHOOTING  
**What it shows:** Real-time logs, HTTP errors, response times

### ✅ TESTED COMMANDS:

**Stream live logs (interactive):**
```powershell
az webapp log tail --name app-frontend-89609 --resource-group rg-copilot-demo
```
*Shows: Real-time application logs*

**Check recent activity log:**
```powershell
az monitor activity-log list --resource-group rg-copilot-demo --max-events 10 --query "[].{Time:eventTimestamp, Operation:operationName.localizedValue, Status:status.localizedValue}" -o table
```
*Shows: Recent operations like restarts, config changes*

**Web app HTTP metrics:**
```powershell
$subId = az account show --query id -o tsv
az monitor metrics list --resource "/subscriptions/$subId/resourceGroups/rg-copilot-demo/providers/Microsoft.Web/sites/app-frontend-89609" --metric "Requests" "Http2xx" "Http4xx" --interval PT5M -o table
```
*Shows: Request counts by status code*

### COPILOT PROMPTS:
- "Stream live logs from my Azure App Service app-frontend-89609"
- "Show me HTTP errors from my Azure web app in rg-copilot-demo"
- "What's the average response time for my Azure App Service?"

---

## DEMO SECTION 3: BACKEND/API INVESTIGATION
**What it shows:** App Insights telemetry, slow endpoints, dependencies

### ✅ TESTED COMMANDS:

**Request summary by status:**
```powershell
az monitor app-insights query --app ai-copilot-demo --resource-group rg-copilot-demo --analytics-query "requests | summarize count() by cloud_RoleName, resultCode | order by count_ desc"
```
*Shows: Count of 200s, 404s, etc. per app*

**Find 404 errors:**
```powershell
az monitor app-insights query --app ai-copilot-demo --resource-group rg-copilot-demo --analytics-query "requests | where resultCode == '404' | project timestamp, url, duration | order by timestamp desc | take 10"
```
*Shows: Recent 404 errors with URLs*

**Average response times per app:**
```powershell
az monitor app-insights query --app ai-copilot-demo --resource-group rg-copilot-demo --analytics-query "requests | summarize avgDuration=avg(duration), count=count() by cloud_RoleName | order by avgDuration desc"
```
*Shows: Backend ~33ms, Frontend ~22ms average*

**Check app settings:**
```powershell
az webapp config appsettings list --name app-backend-89609 --resource-group rg-copilot-demo --query "[].{Name:name, Value:value}" -o table
```
*Shows: All configuration including LOG_LEVEL=DEBUG, VERSION=2.1.0*

### COPILOT PROMPTS:
- "Show me 404 errors from Azure Application Insights ai-copilot-demo"
- "What's the average response time in Azure App Insights for rg-copilot-demo?"
- "Check the Azure App Service app settings for app-backend-89609"

---

## DEMO SECTION 4: DATABASE DIAGNOSTICS
**What it shows:** SQL metrics, DTU usage, firewall rules

### ✅ TESTED COMMANDS:

**SQL DTU metrics:**
```powershell
$subId = az account show --query id -o tsv
az monitor metrics list --resource "/subscriptions/$subId/resourceGroups/rg-copilot-demo/providers/Microsoft.Sql/servers/sql-copilot-demo-89609/databases/appdb" --metric "dtu_consumption_percent" --interval PT5M -o table
```
*Shows: DTU percentage over time (usually 0-1% for idle)*

**SQL Server info:**
```powershell
az sql server show --name sql-copilot-demo-89609 --resource-group rg-copilot-demo --query "{Name:name, FQDN:fullyQualifiedDomainName, State:state, Admin:administrators.login}" -o table
```
*Shows: Server FQDN, Ready state, AD admin*

**Firewall rules:**
```powershell
az sql server firewall-rule list --server sql-copilot-demo-89609 --resource-group rg-copilot-demo -o table
```
*Shows: AllowAzureServices and AllowMyIP rules*

**Database size/usage:**
```powershell
az sql db show --server sql-copilot-demo-89609 --name appdb --resource-group rg-copilot-demo --query "{Name:name, Edition:edition, MaxSize:maxSizeBytes, Status:status}" -o table
```
*Shows: Basic edition, 2GB max, Online status*

### COPILOT PROMPTS:
- "Show me DTU usage for my Azure SQL database appdb in rg-copilot-demo"
- "List firewall rules for my Azure SQL server sql-copilot-demo-89609"
- "What's my Azure SQL database size and status?"

---

## DEMO SECTION 5: CONFIG COMPARISON
**What it shows:** Differences between environments, version drift

### ✅ TESTED COMMANDS:

**Compare settings side-by-side:**
```powershell
Write-Host "=== FRONTEND ===" -ForegroundColor Cyan
az webapp config appsettings list --name app-frontend-89609 --resource-group rg-copilot-demo --query "[?name=='VERSION' || name=='LOG_LEVEL' || name=='CACHE_ENABLED' || name=='FEATURE_FLAG_V2'].{Name:name, Value:value}" -o table

Write-Host "`n=== BACKEND ===" -ForegroundColor Cyan  
az webapp config appsettings list --name app-backend-89609 --resource-group rg-copilot-demo --query "[?name=='VERSION' || name=='LOG_LEVEL' || name=='CACHE_ENABLED' || name=='FEATURE_FLAG_V2'].{Name:name, Value:value}" -o table
```
*Shows these differences:*
| Setting | Frontend | Backend |
|---------|----------|---------|
| VERSION | 2.0.5 | 2.1.0 |
| LOG_LEVEL | INFO | DEBUG |
| CACHE_ENABLED | true | false |
| FEATURE_FLAG_V2 | disabled | enabled |

**App Service Plan info:**
```powershell
az appservice plan show --name asp-copilot-demo --resource-group rg-copilot-demo --query "{Name:name, SKU:sku.name, Workers:sku.capacity}" -o table
```
*Shows: B1 SKU with 1 worker*

### COPILOT PROMPTS:
- "Compare Azure App Service settings between app-frontend-89609 and app-backend-89609"
- "What version is deployed to each Azure web app in rg-copilot-demo?"
- "Show me configuration differences between my Azure App Services"

---

## DEMO SECTION 6: ACTIVITY & AUDIT
**What it shows:** Who did what and when

### ✅ TESTED COMMANDS:

**Recent Azure operations:**
```powershell
az monitor activity-log list --resource-group rg-copilot-demo --max-events 15 --query "[].{Time:eventTimestamp, Operation:operationName.localizedValue, Status:status.localizedValue, Caller:caller}" -o table
```
*Shows: Restart, config updates, deployments with timestamps*

### COPILOT PROMPTS:
- "Show me recent Azure activity log for resource group rg-copilot-demo"
- "Who made changes to my Azure web app app-backend-89609?"
- "When was my Azure App Service last restarted?"

---

## QUICK REFERENCE - WORKING DEMO COMMANDS

```powershell
# Environment overview
az resource list -g rg-copilot-demo -o table

# App status
az webapp list -g rg-copilot-demo --query "[].{Name:name,State:state}" -o table

# Live logs
az webapp log tail --name app-backend-89609 -g rg-copilot-demo

# App Insights - errors
az monitor app-insights query --app ai-copilot-demo -g rg-copilot-demo --analytics-query "requests | where resultCode != '200' | take 10"

# App Insights - performance
az monitor app-insights query --app ai-copilot-demo -g rg-copilot-demo --analytics-query "requests | summarize avg(duration) by cloud_RoleName"

# SQL metrics
az monitor metrics list --resource "/subscriptions/$(az account show --query id -o tsv)/resourceGroups/rg-copilot-demo/providers/Microsoft.Sql/servers/sql-copilot-demo-89609/databases/appdb" --metric "dtu_consumption_percent" -o table

# Config compare
az webapp config appsettings list --name app-backend-89609 -g rg-copilot-demo -o table

# Activity log
az monitor activity-log list -g rg-copilot-demo --max-events 10 -o table
```

---

## 🚀 GRAND FINALE: ONE-SHOT COMPREHENSIVE QUERIES

**What it shows:** The real power of AI - handling complex, multi-part requests in natural language

### THE "DROP THE MIC" MOMENTS

Instead of asking 15 individual questions, try these comprehensive prompts that combine multiple operations:

---

### 🔥 Full Environment Health Check
```
Give me a complete health check of my Azure environment in rg-copilot-demo. 
Check if all resources are running, show me any HTTP errors in the last hour, 
compare the configuration between my frontend and backend apps, and tell me 
if there are any obvious issues I should fix.
```

---

### 🔥 Incident Response Mode
```
I'm getting reports of errors in production. For my rg-copilot-demo environment:
1. Check if all services are running
2. Show me recent errors from Application Insights
3. Check if there's a version mismatch between frontend and backend
4. Look at the activity log for any recent changes that might have caused this
```

---

### 🔥 Configuration Drift Analysis
```
Analyze my Azure App Services app-frontend-89609 and app-backend-89609 for 
configuration drift. Compare their app settings, show me any mismatches in 
VERSION, LOG_LEVEL, CACHE_ENABLED, and FEATURE_FLAG settings, and explain 
what problems these differences might cause.
```

---

### 🔥 Pre-Deployment Checklist  
```
Before I deploy to production, give me a status report on rg-copilot-demo:
- Are both web apps healthy and running?
- What's the current database DTU usage?
- Show me the current versions deployed to each app
- Are there any firewall rules I should review?
- What were the last 5 changes made to this environment?
```

---

### 🔥 Cost & Performance Summary
```
Give me a summary of my rg-copilot-demo resources including:
- What tier/SKU is each resource using?
- Average response times for my web apps
- Database utilization metrics
- Any resources that might be over or under-provisioned
```

---

### 🔥 "2AM Pager Alert" Simulation
```
My app is down and users are complaining. I need you to quickly:
1. Check if my web apps and database in rg-copilot-demo are online
2. Look for any 500 errors or timeouts in the last 30 minutes
3. Check if anyone made configuration changes recently
4. Compare frontend vs backend settings to find any mismatches
5. Give me a summary of what might be wrong and what to fix first
```

---

### KEY TALKING POINT FOR AUDIENCE

> "Notice how I didn't need to remember a single Azure CLI command, Kusto query syntax, 
> or which portal blade to click. I just described what I needed in plain English, and 
> Copilot figured out which 5-6 different Azure APIs to call, correlated the data, 
> and gave me actionable insights. That's the difference between a command-line tool 
> and an AI operations assistant."

---

## CLEANUP AFTER DEMO

```powershell
# Reset simulated issues
.\simulate-issues.ps1 -Issue clear-issues

# Or delete everything (stops all charges)
az group delete --name rg-copilot-demo --yes --no-wait
```
