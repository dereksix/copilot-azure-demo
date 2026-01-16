# Copilot Azure Demo - Presenter Guide
## Quick reference for live demos

---

## BEFORE YOU START
Run this 5 minutes before the demo to generate data:
> "Run the simulate issues script with all issues"

---

## 1. ENVIRONMENT DISCOVERY
**What we're showing:** Copilot can instantly inventory your entire Azure environment

| Say This | What It Does |
|----------|--------------|
| "Show me all resources in my Azure resource group rg-copilot-demo" | Lists all 10 resources (web apps, SQL, storage, etc.) |
| "Check if my Azure App Service web apps are running" | Confirms apps are healthy and running |
| "Is my Azure SQL database online?" | Verifies database connectivity |

---

## 2. FRONTEND TROUBLESHOOTING
**What we're showing:** Real-time visibility into web app health and errors

| Say This | What It Does |
|----------|--------------|
| "Stream live logs from my Azure App Service app-frontend-10084" | Opens real-time log stream |
| "Show me HTTP errors from my Azure web app" | Finds 404s, 500s, timeouts |
| "What's the average response time for my Azure App Service?" | Shows performance metrics |

---

## 3. BACKEND/API INVESTIGATION
**What we're showing:** Deep dive into Application Insights telemetry

| Say This | What It Does |
|----------|--------------|
| "Show me 404 errors from Azure Application Insights ai-copilot-demo" | Lists failed requests with URLs |
| "What's the average response time in Azure App Insights?" | Compares frontend vs backend performance |
| "Check the Azure App Service app settings for app-backend-10084" | Shows all configuration values |

---

## 4. DATABASE DIAGNOSTICS
**What we're showing:** SQL Server monitoring without needing SSMS

| Say This | What It Does |
|----------|--------------|
| "Show me DTU usage for my Azure SQL database appdb" | Shows database resource consumption |
| "List firewall rules for my Azure SQL server" | Displays network security config |
| "What's my Azure SQL database size and status?" | Shows capacity and health |

---

## 5. CONFIG COMPARISON
**What we're showing:** Spot configuration drift between environments

| Say This | What It Does |
|----------|--------------|
| "Compare Azure App Service settings between app-frontend-10084 and app-backend-10084" | Side-by-side config diff |
| "What version is deployed to each Azure web app?" | Shows version mismatch (2.0.5 vs 2.1.0) |
| "Show me configuration differences between my Azure App Services" | Highlights LOG_LEVEL, CACHE, FEATURE_FLAG differences |

---

## 6. ACTIVITY & AUDIT
**What we're showing:** Track changes and troubleshoot "what happened?"

| Say This | What It Does |
|----------|--------------|
| "Show me recent Azure activity log for resource group rg-copilot-demo" | Lists recent operations |
| "Who made changes to my Azure web app?" | Shows caller/user info |
| "When was my Azure App Service last restarted?" | Finds restart events |

---

## CLEANUP
> "Reset the simulated issues"

or

> "Delete the resource group rg-copilot-demo"

---

## 💡 PRO TIPS

- **Pause after each prompt** - let the audience see Copilot thinking
- **Read the command output** - Copilot explains what it's doing
- **Ask follow-ups** - "Why is that?" or "How do I fix it?"
- **Keep generate-load.ps1 running in background** with `-Loop` for live data
