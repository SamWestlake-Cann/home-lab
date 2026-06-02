# Phase 2 — Windows Administration

## Objective
Audit and manage user accounts, configure NTFS folder permissions, explore
Windows administrative tools, simulate real help desk scenarios, run
PowerShell admin commands, and configure Windows Firewall rules on
Windows 11 VM (SAM-LAB-CLIENT01).

## Environment
- **Host:** MacBook Air (192.168.1.106)
- **VM:** Windows 11 — SAM-LAB-CLIENT01 (192.168.64.10 static)
- **Tools used:** PowerShell, Computer Management, Event Viewer, File Explorer, Windows Firewall

## Tasks Completed

### 1. User Account Audit and Management
Reviewed existing local accounts using PowerShell. Found and corrected
a group membership issue where lab_admin was incorrectly in both
Administrators and Users groups. Added descriptions to all accounts.

| Account | Type | Description |
|---|---|---|
| Sam | Administrator | Primary admin account — lab owner |
| lab_admin | Administrator | Secondary admin — lab administration |
| test_user | Standard User | Standard user — permissions testing |
| Administrator | Disabled | Built-in (security best practice) |
| Guest | Disabled | Built-in (security best practice) |

Commands used: Get-LocalUser, Get-LocalGroupMember, Set-LocalUser, Remove-LocalGroupMember

### 2. NTFS Folder Permissions
Created C:\LabFiles with three subfolders and applied NTFS permissions
using icacls to demonstrate principle of least privilege.

| Folder | Access |
|---|---|
| Admin_Only | Admins only |
| Shared | All users |
| Test_User_Docs | test_user + admins |

Verified by logging in as test_user — Admin_Only denied, others accessible.

### 3. Computer Management Tools
Explored all sections of compmgmt.msc:
- Event Viewer — filtered Security log for Event ID 4624, found test_user interactive logon recorded at 7:21 PM on 1/06/2026
- Device Manager — identified unknown VirtIO devices, Red Hat VirtIO Ethernet Adapter and QEMU NVMe virtual disk
- Disk Management — documented disk layout: EFI partition, 63GB C: drive, recovery partition, Windows ISO and UTM Guest Tools
- Services — stopped and restarted Print Spooler service

### 4. Help Desk Tickets
Three tickets raised and resolved based on real lab tasks:
- Ticket 001 — Printer not working (Print Spooler stopped)
- Ticket 002 — Folder access denied (NTFS permissions)
- Ticket 003 — Static IP configuration request

Full tickets in /tickets folder.

### 5. PowerShell Admin Tasks

| Command | Purpose |
|---|---|
| Get-Process | Top 15 processes by CPU |
| Get-Service | All running services |
| Get-PSDrive | Disk space across all drives |
| Get-NetIPConfiguration | Confirmed static IP and DNS |
| Get-Date - LastBootUpTime | System uptime |
| Get-ComputerInfo | OS version, hostname, RAM, CPU |

### 6. Windows Firewall Rules
Verified all three firewall profiles enabled (Domain, Private, Public).
Created, tested and removed an inbound block rule for ICMP ping.

- Created rule blocking inbound ICMPv4 type 8
- Verified: ping from Mac returned 100% packet loss
- Removed rule and confirmed deletion
- Key learning: ping still blocked after rule removal due to NAT — same symptom, different cause

## Key Learnings
- Principle of least privilege applies to both accounts and folder permissions
- PowerShell description fields have a 48 character limit
- NTFS inheritance must be disabled before applying custom permissions
- Event Viewer Security log records every logon — critical for auditing
- Same symptom can have different root causes at different network layers
- GUI changes do not always apply — command line is the reliable fallback
- Windows Firewall and NAT are independent security layers

## Screenshots
All 36 screenshots in this folder, numbered in sequence.
