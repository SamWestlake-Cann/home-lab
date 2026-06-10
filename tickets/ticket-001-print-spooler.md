# Ticket 001 — Printer Not Working

| Field | Detail |
|---|---|
| **Title** | User unable to print documents |
| **Reported by** | test_user |
| **Device** | SAM-LAB-CLIENT01 |
| **Date** | 01/06/2026 |
| **Priority** | Low |
| **Category** | Hardware / Printing |
| **Status** | Resolved |

## Description
User called the help desk reporting they were unable to print any 
documents from their workstation. Printer was visible in the system 
but no jobs were processing and no error message was displayed.

## Investigation
Confirmed the issue was isolated to this workstation only — no other 
users reported printing issues. Asked the user what troubleshooting 
steps they had already attempted. Navigated to Services via Computer 
Management (compmgmt.msc) and identified that the Print Spooler 
service was in a stopped state despite being set to Automatic startup.

## Resolution
Restarted the Print Spooler service via Computer Management → Services. 
Service returned to Running status. Confirmed with user that printing 
was restored and documents were processing correctly.

## Evidence
- Screenshot: 24_print_spooler_stopped.png
- Screenshot: 25_print_spooler_restarted.png
