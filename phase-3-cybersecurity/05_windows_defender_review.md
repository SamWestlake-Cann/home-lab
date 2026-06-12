# Windows Defender Review — Phase 3 Cybersecurity Basics

## Objective
Verify Windows Defender configuration, understand its security posture, run a scan, and review results. Assess how antivirus fits into a layered security model.

## Environment
- **Host:** MacBook Air (192.168.1.106)
- **VM:** Windows 11 — SAM-LAB-CLIENT01 (192.168.64.10)
- **Review Date:** 8/06/2026

---

## Methods

### 1. Configuration Baseline Review
**Command:** Check core Windows Defender security settings via PowerShell

```powershell
Get-MpPreference | 
Format-List `
DisableRealtimeMonitoring,
DisableBehaviorMonitoring,
PUAProtection,
SubmitSamplesConsent,
HighThreatDefaultAction
```

**Screenshot:** `05_windows_defender_config_baseline.png`

**Findings:**
| Setting | Value | Status |
|---------|-------|--------|
| Real-time Monitoring | False (Enabled) | ✅ Active |
| Behavior Monitoring | False (Enabled) | ✅ Active |
| PUA Protection | 2 (Block) | ✅ Strict |
| Submit Samples | 1 (Safe Only) | ✅ Conservative |
| High Threat Action | 0 (Quarantine) | ✅ Safe |

**Assessment:** Real-time protection and behavior monitoring are both enabled, providing active threat detection. PUA protection at level 2 (Block) prevents potentially unwanted applications. High threats are quarantined rather than deleted, allowing recovery if needed.

---

### 2. Scan & Signature Status
**Command:** Check antivirus signature currency and scan history

```powershell
Get-MpComputerStatus | 
Format-List `
AntivirusSignatureAge,
AntivirusSignatureVersion,
FullScanAge,
QuickScanAge,
AMRunningMode
```

**Screenshot:** `06_windows_defender_scan_status.png`

**Findings:**
| Setting | Value | Status |
|---------|-------|--------|
| Signature Age | 0 days | ✅ Current |
| Signature Version | 1.451.324.0 | ✅ Up-to-date |
| Quick Scan Age | 6 days | ✅ Recent |
| Full Scan Age | Never/Very Old | ⚠️ Not recently done |
| Running Mode | Normal | ✅ Active |

**Assessment:** Malware signatures are completely up-to-date (0 days old). Quick scans have run recently (6 days ago). No recent full scan recorded, which is a gap we will address with a full scan.

---

### 3. Threat Detection History
**Command:** Review any detected threats in the system

```powershell
Get-MpThreatDetection | 
Select-Object `
-First 20 `
ThreatName,
ThreatID,
Resources,
Severity
```

**Screenshots:** 
- `07_powershell_head_command_error.png` (Learning: Linux `Head` command doesn't work in PowerShell)
- `08_windows_defender_threat_history.png` (Corrected command output)

**Findings:**
- **No threats detected** — System is clean
- Threat history is empty
- No malware, PUAs, or suspicious files recorded

**Assessment:** The system has not encountered any threats. Combined with current scans, this indicates a clean security posture.

---

## Learning Notes

### PowerShell vs. Linux Commands
During this task, I attempted to use the Linux `Head` command in PowerShell:
```powershell
Get-MpThreatDetection | Format-List | Head -20  # ❌ WRONG
```

This failed because `Head` is a Linux/bash command. PowerShell uses `Select-Object -First`:
```powershell
Get-MpThreatDetection | Select-Object -First 20  # ✅ CORRECT
```

**Key Learning:** Always verify which shell/environment you're in. Different command-line tools have different syntax and cmdlets.

---

## Full Scan Results

**Command:** Run comprehensive Windows Defender full scan via Windows Security UI

**Screenshot:** `09_windows_defender_full_scan_results.png`

**Findings:**
| Metric | Result | Status |
|--------|--------|--------|
| Threats Detected | 0 | ✅ Clean |
| Files Scanned | 297,328 | ✅ Comprehensive |
| Scan Duration | 27 min 22 sec | ✅ Complete |
| Scan Type | Full Scan | ✅ Thorough |
| Scan Date/Time | 8/06/2026 8:15 PM | ✅ Recent |

**Assessment:** Full scan confirms zero threats across all 297,328 files on the system. This comprehensive scan validates the earlier quick scan results and demonstrates a secure system state.
---

## Conclusion

**Windows Defender Configuration Status: SECURE** ✅

- Real-time and behavior monitoring are active
- Malware signatures are current
- No threats detected in system history
- PUA protection enabled at highest level
- Threat response actions properly configured

**Next Steps:**
1. Complete full scan and review results
2. Move to CIS Benchmark hardening in Phase 3
3. Document any findings or configuration changes

---

## Files Referenced
- `05_windows_defender_config_baseline.png`
- `06_windows_defender_scan_status.png`
- `07_powershell_head_command_error.png`
- `08_windows_defender_threat_history.png`