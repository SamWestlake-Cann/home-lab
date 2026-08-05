# Phase 5b — Microsoft Sentinel (Cloud SIEM)

## Overview
Hands-on lab deploying Microsoft Sentinel as a cloud-based SIEM,
paired against the existing on-prem Wazuh SIEM built in Phase 4.
Covers workspace setup, VM data source onboarding, custom analytics
rules, and detection testing — mirroring the T1110 brute-force
detection built on Wazuh for direct comparison.

## Environment
- Azure Free Account
- Tenant: samwestlakecann1gmail.onmicrosoft.com
- Log Analytics Workspace: law-phase5-sentinel (Australia East)
- Resource group: rg-phase5-sentinel
- VM: vm-phase5-sentinel-win, Windows Server 2025 Datacenter (Gen2),
  Standard_B2ats_v2

## Tasks
- [x] Create Log Analytics Workspace
- [x] Enable Microsoft Sentinel
- [x] Deploy Windows VM as data source
- [x] Connect VM to Sentinel
- [x] Build analytics rules (failed RDP, suspicious PowerShell)
- [x] Trigger and validate detections
- [ ] Write on-prem (Wazuh) vs cloud (Sentinel) comparison

## Cost notes
- Log Analytics Workspace: free daily ingestion allowance on first workspace
- VM: Standard_B2ats_v2, ~$0.0211 USD/hr, free-services eligible —
  deallocate (not just stop) when not in active use
- Auto-shutdown configured: 7:00 PM Sydney time
- Budget alert active: $150 threshold, 50/80/100% alerts

## Log

### 5 Aug 2026 — Setup
- Created phase-5b-sentinel folder structure

### 5 Aug 2026 — Workspace, Sentinel, VM deployment
- Created Log Analytics Workspace "law-phase5-sentinel" (Australia East,
  resource group rg-phase5-sentinel)
- Screenshot: 29-log-analytics-workspace-created.png
- Enabled Microsoft Sentinel on the workspace — noted Sentinel management
  has moved to the unified Defender portal (security.microsoft.com)
  rather than the classic Azure portal
- Screenshot: 30-sentinel-enabled-workspace.png

### 5 Aug 2026 — VM deployment troubleshooting
- Deployed Windows Server 2025 Datacenter (Gen2) VM, size Standard_B2ats_v2
  (free-tier eligible, ~$0.0211/hr)
- Hit two blockers during creation:
  1. Standard_B1s unavailable for subscription in Australia East —
     resolved by selecting Standard_B2ats_v2 instead
  2. "Subscription requires no spending limit" warning on Windows 11 25H2
     image — resolved by switching to Windows Server 2025 Datacenter
     rather than removing the free trial's spending limit
- Configured auto-shutdown safety net (7:00 PM Sydney time, corrected
  from an initial UTC misconfiguration)
- Deallocated VM at end of session to avoid ongoing compute charges
- Screenshots: 32-vm-deployment-succeeded.png, 33-vm-stopped-deallocated.png

### 6 Aug 2026 — VM connected to Sentinel
- Created Data Collection Rule "dcr-phase5-windows-security" via the
  Windows Security Events via AMA connector, scoped to vm-phase5-sentinel-win
- Collection type: All Security Events (AllEvents filter)
- Navigating the Defender portal's connector setup required several
  attempts — Sentinel's UI has moved significantly from the classic
  Azure portal experience (now split across Content Hub, Data
  Connectors, and per-connector detail panes)
- Confirmed data flowing: 182 SecurityEvents ingested within minutes
  of DCR creation
- Screenshot: 34-dcr-created-data-flowing.png

### 6 Aug 2026 — Analytics rule built and detection test triggered
- Enabled built-in analytics rule template "Excessive Windows Logon
  Failures" (MITRE T1110, Credential Access), mirroring the Wazuh
  brute-force detection from Phase 4
- Customized for lab testing: reduced countlimit threshold from 50 to 5
  failures, tightened query scheduling to run every 5 minutes looking
  back 1 hour (defaults were daily, impractical for live testing)
- Rule created and enabled successfully
- RDP'd into vm-phase5-sentinel-win using the Windows App (Mac RDP client)
- Triggered 6 deliberate failed logon attempts (EventID 4625) against
  the samadmin account, then logged in successfully to confirm no lockout
- Verified via Advanced Hunting KQL query that all 6 EventID 4625 events
  landed correctly in Log Analytics within minutes
- Screenshots: 35-analytics-rule-created-enabled.png,
  36-vm-running-public-ip.png, 37-failed-logon-events-confirmed.png
- Awaiting scheduled rule run to confirm incident generation — note
  samadmin is a local admin account, so AccountType filter in the rule
  query (=~ "User") may need adjustment if no incident fires

### 6 Aug 2026 — Incident confirmed, detection validated end-to-end
- Analytics rule fired successfully: Incident ID 1 "Excessive Windows
  Logon Failures" created automatically, Severity Low, Category
  Credential Access
- Incident graph shows full entity chain: vm-phase5-sentinel-win (device)
  -> svchost.exe (process) -> samadmin (account), first activity 8:45 AM,
  last activity 9:45 AM, 1/1 active alerts
- Confirms full detection pipeline working end-to-end: VM -> Data
  Collection Rule -> Log Analytics -> Analytics Rule -> Incident
- AccountType filter concern (samadmin being a local admin account)
  turned out to be a non-issue — rule matched correctly regardless
- Deallocated VM at end of session
- Screenshots: 38-incident-generated-excessive-logon-failures.png,
  39-incident-details-attack-story-graph.png

## Next session
- Write on-prem (Wazuh) vs cloud (Sentinel) comparison document —
  have strong material now: both detections cover T1110 brute force,
  same underlying attack pattern (failed logons), different platforms
  and detection mechanics (Wazuh custom rule vs Sentinel KQL scheduled
  query), worth comparing setup complexity, cost, alert quality, and
  investigation experience (Wazuh dashboard vs Sentinel incident graph)
