# Get-AbrAzDesktopVirtualization.ps1

Development repo for adding Azure Virtual Desktop (AVD) reporting to the [AsBuiltReport.Microsoft.Azure](https://github.com/AsBuiltReport/AsBuiltReport.Microsoft.Azure) module. The code here will be contributed upstream via PR.

## Current Version: v0.1.1

Single consolidated function `Get-AbrAzDesktopVirtualization` covering all AVD resource types with four InfoLevel tiers:

| InfoLevel | Content |
|-----------|---------|
| **1** | Summary tables for host pools, application groups, workspaces, scaling plans |
| **2** | Per-host-pool detail sections with RDP properties, agent update config, session host tables with health checks |
| **3** | Registration token info, per-app-group detail with published applications (RemoteApp), per-scaling-plan schedule breakdowns |
| **4** | Per-session-host vertical detail sections (OS, VM ID, update state), active user sessions per host pool |

### Health Checks

| Check | Condition | Style |
|-------|-----------|-------|
| Session Host Unavailable | Status != "Available" | Warning |
| Session Host Drain Mode | AllowNewSession = false | Warning |
| Registration Token Expired | Token past expiration | Warning |
| No Session Hosts | Host pool has 0 session hosts | Warning (bold paragraph) |
| Host Pool at Capacity | Sessions >= MaxSessionLimit | Warning (bold paragraph) |

### Azure Cmdlets Used

- `Get-AzWvdHostPool`, `Get-AzWvdSessionHost`, `Get-AzWvdUserSession`
- `Get-AzWvdApplicationGroup`, `Get-AzWvdApplication`
- `Get-AzWvdWorkspace`, `Get-AzWvdScalingPlan`

## Integration

To integrate into the installed `AsBuiltReport.Microsoft.Azure` module:

1. Copy `Src/Private/Get-AbrAzDesktopVirtualization.ps1` to module's `Src/Private/`
2. Add `"DesktopVirtualization" = "Get-AbrAzDesktopVirtualization"` to `$SectionFunctionMap` in the orchestrator
3. Add `"DesktopVirtualization"` to `$DefaultSectionOrder` in the orchestrator
4. Add `DesktopVirtualization` entries to module JSON: `SectionOrder`, `InfoLevel`, `HealthCheck`

## Requirements

- PowerShell 7+ (`pwsh`)
- `Az.DesktopVirtualization` module
- `AsBuiltReport.Microsoft.Azure` v0.2.0+
