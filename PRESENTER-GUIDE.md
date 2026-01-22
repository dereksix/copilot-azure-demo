# Copilot Azure Demo - Presenter Guide
## Quick reference for live demos

---

## � SETTING THE STAGE (2-5 minutes)

### Opening Hook
*Start with a relatable scenario:*

> "Raise your hand if you've ever been woken up at 2 AM by a PagerDuty alert. Your production app is down. Users are angry. Your boss is asking for updates. And you're sitting there, half-awake, trying to remember... what's the Azure CLI command to check my App Service logs again?"

*Pause for effect.*

> "Now imagine instead of frantically Googling commands or clicking through 47 portal blades, you just... ask. In plain English. 'What's wrong with my web app?' And an AI assistant that actually understands your Azure environment tells you — and shows you how to fix it."

### The Pain Points (The "Old Way")
*Connect with your audience's frustrations:*

> "Let's be honest about Azure operations today:"

| The Reality | The Pain |
|-------------|----------|
| **100+ Azure services** | Each with its own CLI syntax, portal blade, and quirks |
| **Kusto Query Language** | Powerful, but who has time to learn another query language? |
| **Configuration drift** | "It works on my machine" now applies to cloud environments |
| **Portal fatigue** | Click... click... wait... click... where was that setting again? |
| **Tribal knowledge** | Your best engineer is also your single point of failure |

> "We've all been there. You know the *what* — 'I need to check my database metrics.' But the *how* requires memorizing syntax like `az monitor metrics list --resource /subscriptions/abc123/resourceGroups/...` — and honestly, life's too short."

### The Promise (What Changes Today)
*Build anticipation:*

> "What if your cloud operations looked like this instead?"

- ❌ ~~`az webapp log tail --name app-frontend-89609 --resource-group rg-copilot-demo --provider http`~~
- ✅ **"Show me live logs from my frontend app"**

- ❌ ~~`az monitor app-insights query --app ai-copilot-demo -g rg-copilot-demo --analytics-query "requests | where resultCode >= 400 | project timestamp, url, resultCode"`~~
- ✅ **"What HTTP errors happened in the last hour?"**

> "GitHub Copilot isn't just for writing code anymore. It's an AI operations assistant that understands Azure, translates your intent into the right commands, and executes them — all through natural conversation."

### What You'll See Today
*Set expectations:*

> "In the next 15-20 minutes, I'm going to troubleshoot a real production issue — live. No slides. No scripts. Just me, Copilot, and an Azure environment with some... let's call them 'intentional learning opportunities.'"

**Our journey:**
1. 🔍 **Discovery** — "What do we have deployed?"
2. 🌐 **Frontend** — "Are users seeing errors?"
3. ⚙️ **Backend/API** — "What's happening under the hood?"
4. 🗄️ **Database** — "Is SQL the bottleneck?"
5. ⚖️ **Config Comparison** — "Why does staging work but production doesn't?"
6. 📋 **Audit Trail** — "Who changed what, and when?"

> "By the end, you'll see how Copilot transforms Azure operations from 'command memorization' to 'just asking.' Ready? Let's break some things."

---

## �🎯 WHAT WE'RE DEMONSTRATING

**GitHub Copilot as your AI-powered Azure operations assistant.**

Instead of memorizing dozens of Azure CLI commands, navigating multiple portal blades, or writing complex Kusto queries — you just *ask* Copilot in plain English. It understands your Azure environment and executes the right commands for you.

### The Demo Environment

We've built a realistic 3-tier web application in Azure:

| Component | Resource | Purpose |
|-----------|----------|---------|
| **Frontend** | `app-frontend-89609` | Web UI serving users |
| **Backend API** | `app-backend-89609` | REST API handling business logic |
| **Database** | `sql-copilot-demo-89609/appdb` | Azure SQL storing application data |
| **Monitoring** | `ai-copilot-demo` | Application Insights collecting telemetry |

**Intentional issues baked in:**
- Version mismatch between frontend (v2.0.5) and backend (v2.1.0)
- Different log levels (INFO vs DEBUG)
- Feature flags out of sync
- Simulated HTTP errors and slow responses

### What Makes This Impressive

1. **No memorization** — Natural language replaces complex CLI syntax
2. **Cross-service queries** — Copilot correlates data across App Service, SQL, App Insights
3. **Instant expertise** — Even complex Kusto queries for App Insights are generated on the fly
4. **Real troubleshooting** — Find actual issues, not just "hello world" demos

---

## BEFORE YOU START

Run this 5 minutes before the demo to generate realistic traffic and errors:

> "Run the simulate issues script with all issues"

**Or for continuous background traffic:**
> "Run generate-load.ps1 with the loop flag"

---

## 1. ENVIRONMENT DISCOVERY

### 🎬 The Scenario
*"I just got paged. Something's wrong in production. I need to quickly understand what we have deployed and if things are running."*

### Why This Matters
Traditionally, you'd need to open the Azure Portal, navigate to the resource group, click through each resource, or remember `az resource list` syntax. With Copilot, just ask.

| Say This | What It Does |
|----------|--------------|
| "Show me all resources in my Azure resource group rg-copilot-demo" | Lists all 10 resources (web apps, SQL, storage, etc.) |
| "Check if my Azure App Service web apps are running" | Confirms apps are healthy and running |
| "Is my Azure SQL database online?" | Verifies database connectivity |

---

## 2. FRONTEND TROUBLESHOOTING

### 🎬 The Scenario
*"Users are reporting errors. I need to see what's happening in real-time and check for HTTP failures."*

### Why This Matters
Live log streaming and HTTP metrics usually require navigating to Diagnose and Solve Problems, Metrics blade, or running `az webapp log tail` with the right flags. Copilot handles it conversationally.

| Say This | What It Does |
|----------|--------------|
| "Stream live logs from my Azure App Service app-frontend-89609" | Opens real-time log stream |
| "Show me HTTP errors from my Azure web app" | Finds 404s, 500s, timeouts |
| "What's the average response time for my Azure App Service?" | Shows performance metrics |

---

## 3. BACKEND/API INVESTIGATION

### 🎬 The Scenario
*"The frontend looks fine, but the API might be the problem. I need to dig into Application Insights to find failed requests and slow endpoints."*

### Why This Matters
Application Insights is powerful but intimidating. Writing Kusto queries like `requests | where resultCode == '404' | project timestamp, url, duration` takes expertise. Copilot writes these queries for you — just describe what you want to find.

| Say This | What It Does |
|----------|--------------|
| "Show me 404 errors from Azure Application Insights ai-copilot-demo" | Lists failed requests with URLs |
| "What's the average response time in Azure App Insights?" | Compares frontend vs backend performance |
| "Check the Azure App Service app settings for app-backend-89609" | Shows all configuration values |

---

## 4. DATABASE DIAGNOSTICS

### 🎬 The Scenario
*"Could this be a database issue? I need to check if SQL is overwhelmed, look at DTU usage, and verify the firewall isn't blocking anything."*

### Why This Matters
Database troubleshooting often means opening SSMS, connecting with credentials, running DMV queries. With Copilot, you get SQL metrics, firewall rules, and database health without leaving your terminal — and without needing DBA-level knowledge.

| Say This | What It Does |
|----------|--------------|
| "Show me DTU usage for my Azure SQL database appdb" | Shows database resource consumption |
| "List firewall rules for my Azure SQL server" | Displays network security config |
| "What's my Azure SQL database size and status?" | Shows capacity and health |

---

## 5. CONFIG COMPARISON

### 🎬 The Scenario
*"Wait — are both apps on the same version? Let me compare configurations to find drift between environments."*

### Why This Matters
Configuration drift is a silent killer in production. This demo reveals that frontend is on v2.0.5 while backend is on v2.1.0, log levels don't match, and feature flags are inconsistent. Finding these differences manually would take clicking through each app's Configuration blade. Copilot finds them in seconds.

**🔥 What the audience will see:**
| Setting | Frontend | Backend | Problem |
|---------|----------|---------|---------|
| VERSION | 2.0.5 | 2.1.0 | ⚠️ Mismatch! |
| LOG_LEVEL | INFO | DEBUG | Different verbosity |
| CACHE_ENABLED | true | false | Inconsistent |
| FEATURE_FLAG_V2 | disabled | enabled | Could cause errors |

| Say This | What It Does |
|----------|--------------|
| "Compare Azure App Service settings between app-frontend-89609 and app-backend-89609" | Side-by-side config diff |
| "What version is deployed to each Azure web app?" | Shows version mismatch (2.0.5 vs 2.1.0) |
| "Show me configuration differences between my Azure App Services" | Highlights LOG_LEVEL, CACHE, FEATURE_FLAG differences |

---

## 6. ACTIVITY & AUDIT

### 🎬 The Scenario
*"Who changed what? When did this start? I need to trace back recent changes to find the root cause."*

### Why This Matters
The classic "it worked yesterday" investigation. Activity logs tell you who restarted the app, when configs changed, and what deployments happened. This is critical for incident response and compliance — and Copilot makes it conversational rather than query-based.

| Say This | What It Does |
|----------|--------------|
| "Show me recent Azure activity log for resource group rg-copilot-demo" | Lists recent operations |
| "Who made changes to my Azure web app?" | Shows caller/user info |
| "When was my Azure App Service last restarted?" | Finds restart events |

---

## 🎤 CLOSING TALKING POINTS

**What we just demonstrated:**
- ✅ Full environment inventory in seconds
- ✅ Real-time log streaming with one sentence
- ✅ Complex App Insights queries without knowing Kusto
- ✅ Database diagnostics without SSMS
- ✅ Configuration drift detection across environments  
- ✅ Audit trail investigation for incident response

**The key takeaway:**
> "Copilot doesn't just autocomplete code — it's an AI operations assistant that understands your Azure environment and can execute complex troubleshooting workflows through natural conversation."

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
