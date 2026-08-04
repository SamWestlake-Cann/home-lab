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
- Log Analytics Workspace: TBD
- VM: Windows Server/Client, B1S (free-tier eligible)

## Tasks
- [ ] Create Log Analytics Workspace
- [ ] Enable Microsoft Sentinel
- [ ] Deploy Windows VM as data source
- [ ] Connect VM to Sentinel
- [ ] Build analytics rules (failed RDP, suspicious PowerShell)
- [ ] Trigger and validate detections
- [ ] Write on-prem (Wazuh) vs cloud (Sentinel) comparison

## Cost notes
- Log Analytics Workspace: free daily ingestion allowance on first workspace
- VM: B1S covered under 750 free hours/month — deallocate (not just stop)
  when not in active use
- Budget alert active: $150 threshold, 50/80/100% alerts

## Log

### 5 Aug 2026 — Setup
- Created phase-5b-sentinel folder structure
