# Phase 3 — Cybersecurity Basics

## Objective
Apply early cybersecurity practices to the home lab environment including user privilege auditing, Windows Event Log security analysis, vulnerability scanning with Nmap, applying security hardening based on CIS benchmarks, and configuring Windows Defender.

## Environment
- **Host:** MacBook Air (192.168.1.106)
- **VM:** Windows 11 — SAM-LAB-CLIENT01 (192.168.64.10 static)
- **Tools:** PowerShell, Event Viewer, Nmap, Windows Defender, Windows Security Policy

## Tasks Completed

### ✅ 1. Least Privilege Audit
Review and document current user permissions against principle of least privilege. Identified and documented user accounts and access levels.
**Files:** 01_user_account_security_audit.png, 02_password_required_fixed.png

### ✅ 2. Windows Event Log — Security Analysis
Reviewed Security event log for suspicious activity. Filtered for failed logon attempts (Event ID 4625) and analyzed account changes.
**Files:** 03_event_4625_failed_logon_filter.png, 04_event_4625_failed_logon_detail.png

### ✅ 3. Nmap Vulnerability Scan
Ran Nmap from Mac host against Windows VM. Scanned top 1000 ports, identified all ports filtered by Windows Firewall (0 open ports). Documented findings and assessed secure exposure.
**Files:** 10_nmap_vulnerability_scan.md, 10_nmap_scan_results.png

### ✅ 4. CIS Benchmark Hardening
Applied 5 settings from the CIS Windows 11 benchmark: disabled SMBv1, enabled firewall logging (all profiles), enabled UAC prompt, disabled AutoPlay, disabled LLMNR. Includes troubleshooting of two failed attempts (firewall logging command syntax, AutoPlay registry path) before achieving verified success.
**Files:** 11_cis_benchmark_hardening.md, 11_cis_disable_smbv1.png, 12_cis_firewall_logging_error.png, 13_cis_firewall_logging_success.png, 14_cis_firewall_logging_verified.png, 15_cis_uac_verified.png, 16_cis_autoplay_complete.png, 17_cis_llmnr_verified.png

### ✅ 5. Windows Defender Review
Verified Windows Defender configuration (real-time monitoring, behavior analysis enabled). Ran full system scan (297,328 files, 0 threats). Reviewed security posture and antivirus fit in layered defense model.
**Files:** 05_windows_defender_review.md, 06_windows_defender_scan_status.png, 07_powershell_head_command_error.png, 08_windows_defender_threat_history.png, 09_windows_defender_full_scan_results.png

## Status
✅ Complete — 5 of 5 tasks completed

## Screenshots
All screenshots in this folder, numbered in sequence.
