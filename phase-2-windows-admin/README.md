# Phase 2 — Windows Administration

## Objective
Audit and manage user accounts, configure NTFS folder permissions, explore
Windows administrative tools, and simulate real help desk scenarios on
Windows 11 VM (SAM-LAB-CLIENT01).

## Environment
- **Host:** MacBook Air (192.168.1.106)
- **VM:** Windows 11 — SAM-LAB-CLIENT01 (192.168.64.10 static)
- **Tools used:** PowerShell (Administrator), Computer Management, Event Viewer, File Explorer

## Tasks Completed

### 1. User Account Audit and Management
Reviewed existing local accounts using PowerShell. Found and corrected
a group membership issue where lab_admin was incorrectly in both
Administrators and Users groups.

| Account | Type | Description |
|---|---|---|
| Sam | Administrator | Primary admin account — lab owner |
| lab_admin | Administrator | Secondary admin — lab administration |
| test_user | Standard User | Standard user — permissions testing |
| Administrator | Disabled | Built-in (security best practice) |
| Guest | Disabled | Built-in (security best practice) |

**Commands used:**
- `Get-LocalUser` — audit all accounts
- `Get-LocalGroupMember` — check group memberships
- `Set-LocalUser` — add descriptions to accounts
- `Remove-LocalGroupMember` — remove lab_admin from Users group

**Error encountered:** Description character limit of 48 characters —
shortened description and reran successfully. Documented as evidence
of real troubleshooting.

### 2. NTFS Folder Permissions
Created a folder structure at C:\LabFiles and applied NTFS permissions
using icacls to restrict access by user — demonstrating the principle
of least privilege.

| Folder | Who can access |
|---|---|
| Admin_Only | Admins (Sam, lab_admin) only |
| Shared | All users |
| Test_User_Docs | test_user + admins |

**Commands used:**
- `New-Item` — create folder structure
- `icacls /inheritance:d` — disable permission inheritance
- `icacls /remove` — remove user group access
- `icacls /grant` — grant specific user access

**Verified** by logging in as test_user and confirming:
- Admin_Only → ❌ Access denied
- Shared → ✅ Accessible
- Test_User_Docs → ✅ Accessible

### 3. Event Viewer — Security Log Analysis
Opened Computer Management and navigated to Windows Logs → Security.
Filtered Event ID 4624 (successful logon) for test_user account.

Found 3 matching events confirming test_user interactive logon
(Logon Type 2) recorded on 1/06/2026 at 7:21 PM — exactly when
account switch was performed during permissions testing.

**Key Event IDs:**
- `4624` — Successful logon
- `4672` — Special privileges assigned (admin logon)

### 4. Device Manager
*In progress*

## Key Learnings
- Principle of least privilege — every account and folder has exactly
  the access it needs, nothing more
- PowerShell description fields have a 48 character limit — always
  test commands before deploying in production
- NTFS permissions require inheritance to be disabled before custom
  rules apply cleanly
- Event Viewer Security log records every logon — critical for
  incident response and auditing
- GUI changes don't always apply immediately — command line is the
  reliable fallback

## Screenshots
All screenshots are in this folder, numbered in sequence (01-18).
