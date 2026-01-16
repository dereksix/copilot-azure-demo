# Copilot Instructions for Azure Troubleshooting Demo

Origin repository: https://github.com/cajetzer/copilot-azure-demo

## 🎯 Project Purpose

This is an **interactive Azure troubleshooting demonstration** that shows how GitHub Copilot in VS Code terminals transforms infrastructure diagnostic workflows from CLI-memorization to natural conversation. The project deploys a 3-tier Azure application (frontend → backend → SQL) with embedded issues for realistic troubleshooting.

**Target Use Case**: Engineer troubleshoots Azure infrastructure issues conversationally—"Show me HTTP errors" rather than "run az monitor app-insights query with these 4 parameters."

---

## 🏗️ Code Structure

**Scripts** (all PowerShell, all from repo root):
- `deploy.ps1` - Creates 8 Azure resources in sequence, outputs `env-config.txt` with resource names
- `simulate-issues.ps1` - Interactive menu or `-Issue` parameter to create observable problems (errors, latency, restarts, config changes)
- `generate-load.ps1` - Parallel HTTP jobs to populate Application Insights with traffic/error data

**Configuration**:
- `env-config.txt` - Generated once by `deploy.ps1`, sourced by all downstream scripts. Contains resource names with random suffix (avoids naming conflicts in shared subscriptions)

**Data Flow**:
```
User Terminal + Copilot
  ↓
  Script loads env-config.txt variables
  ↓
  PowerShell → Azure CLI commands
  ↓
  Azure Resources respond with metrics/logs
  ↓
  Copilot interprets output for user
```

**Key Design Patterns**: 
- All resources are cheap tiers (B1 App Service, Basic SQL) to minimize demo costs
- All resource names are dynamic (generated with random suffix). No hardcoded names. This allows multiple demos to run in same subscription without conflicts.

---

## 📋 Critical Developer Workflows

### 1. **Initial Setup** → `deploy.ps1`
```powershell
.\deploy.ps1
```
### 2. **Generate Realistic Scenarios** → `simulate-issues.ps1`
```powershell
.\simulate-issues.ps1 -Issue http-errors        # 404/500 errors
.\simulate-issues.ps1 -Issue high-latency       # Slow responses
.\simulate-issues.ps1 -Issue app-restart        # Brief outage
.\simulate-issues.ps1 -Issue config-change      # Settings modification
.\simulate-issues.ps1 -Issue all-issues         # Multiple issues
.\simulate-issues.ps1 -Issue clear-issues       # Reset
```
- Creates observable problems for Copilot to diagnose
- **Must run 2-5 min before demo** so Application Insights populates data
- Issues are *temporary* and reversible (except logs which accumulate)

### 3. **Load/Traffic Generation** → `generate-load.ps1`
```powershell
.\generate-load.ps1 -Scenario traffic           # HTTP requests
.\generate-load.ps1 -Scenario errors            # 404/500s
.\generate-load.ps1 -Scenario slow              # High-latency requests
.\generate-load.ps1 -Scenario sql               # Database activity
.\generate-load.ps1 -Scenario logs              # Log entries
.\generate-load.ps1 -Scenario all               # All scenarios
```
- Generates activity visible in logs/metrics
- Used to populate Application Insights before demo (lazy data loading)

### 4. **Cleanup** → Delete resource group
```powershell
az group delete --name $RESOURCE_GROUP --yes --no-wait
```
- Stops all charges (resources persist for ~1 hour after deletion begins)
- **CRITICAL**: Demo resources cost ~$15-25 for 5 days if left running

---

## 🔧 Project-Specific Conventions

### Environment Configuration Pattern
All PowerShell scripts follow this convention:
```powershell
# Load once at script start (already in deploy.ps1, generate-load.ps1, simulate-issues.ps1):
if (Test-Path "env-config.txt") {
    # Parse env-config.txt into PowerShell variables
}
```

**Why**: Avoid hardcoding resource names; `deploy.ps1` generates unique names with random suffix. All downstream scripts reference variables from `env-config.txt`.

**Files using this pattern**: [deploy.ps1](deploy.ps1#L1), [generate-load.ps1](generate-load.ps1#L10), [simulate-issues.ps1](simulate-issues.ps1#L10)

```instructions
# Copilot Instructions for Azure Troubleshooting Demo

## Purpose

Concise guidance for AI agents working with this demo repository. Focus: how to use the scripts, where configuration lives, and the minimal troubleshooting steps agents should try before making changes.

## Code Structure

- `deploy.ps1` — deploys resources and writes `env-config.txt` (unique suffixes per run).
- `simulate-issues.ps1` — creates observable problems (http-errors, high-latency, config changes, restarts).
- `generate-load.ps1` — generates traffic and telemetry to populate Application Insights.
- `env-config.txt` — single source of truth for resource names/URLs; always load before running scripts.

## Critical Workflows (copyable)

Deploy:
```powershell
.\deploy.ps1
```
Simulate issues (example):
```powershell
.\simulate-issues.ps1 -Issue all-issues
```
Generate load (example):
```powershell
.\generate-load.ps1 -Scenario all
```
Cleanup:
```powershell
az group delete --name $RESOURCE_GROUP --yes --no-wait
```

## Project Conventions (essentials)

- Scripts parse `env-config.txt` at start; do not hardcode resource names.
- Output uses visible colors (RED/Green) for errors/completion — scripts favor visibility over silent failures.
- Resource names use the pattern `{type}-copilot-demo-{SUFFIX}` to avoid collisions.

## Integration Points (short)

- Application Insights: created by `deploy.ps1`; query with `az monitor app-insights query`.
- Azure SQL: backend gets `SQL_CONNECTION_STRING` from `env-config.txt`; firewall rules added by `deploy.ps1`.
- Logging: `az webapp log tail --name <app> --resource-group $RESOURCE_GROUP` streams App Service logs.
- Activity Log: `az monitor activity-log list` shows restarts/config changes.

## Demo Prompts

Use `DEMO-PROMPTS.md` as the authoritative list of Copilot prompts and verified commands.

## Common Pitfalls (quick fixes)

- Missing `env-config.txt`: run `.\deploy.ps1` from the repo root.
- Empty App Insights responses: run `.\simulate-issues.ps1 -Issue all-issues` and wait 2–5 minutes.
- SQL firewall issues: re-run `deploy.ps1` or add a rule with `az sql server firewall-rule create`.
- Unexpected costs: delete the resource group to stop charges.

## Notes for AI Agents

- Preserve demo intent — this repo is for interactive troubleshooting demonstrations.
- Always load `env-config.txt` before executing commands.
- Keep edits idempotent and locally verifiable; test `az` commands before committing changes.

## Quick Workflow

1. Load `env-config.txt`.
2. Ask Copilot a natural question; review the generated `az`/PowerShell command.
3. Execute and inspect logs/App Insights/activity log.

## Support

Reset simulated issues:
```powershell
.\simulate-issues.ps1 -Issue clear-issues
```
Tear down demo:
```powershell
az group delete --name $RESOURCE_GROUP --yes
```

```
| [simulate-issues.ps1](simulate-issues.ps1) | Create observable problems | Interactive menu or `-Issue` parameter, cloud-native issues |
