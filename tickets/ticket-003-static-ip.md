# Ticket 003 — Static IP Configuration Request

| Field | Detail |
|---|---|
| **Title** | Static IP configuration request — SAM-LAB-CLIENT01 |
| **Reported by** | lab_admin |
| **Device** | SAM-LAB-CLIENT01 |
| **Date** | 26/05/2026 |
| **Priority** | Medium |
| **Category** | Network / Configuration |
| **Status** | Resolved |

## Description
Request submitted by lab_admin to assign a static IP address to 
SAM-LAB-CLIENT01. Machine was obtaining its IP automatically via DHCP 
from the UTM virtual router (192.168.64.1). A static IP was required 
to ensure monitoring tools and remote access could reliably connect 
to the machine without the IP changing between sessions.

## Investigation
Ran ipconfig /all to document the current network configuration. 
Confirmed DHCP was enabled and the machine had been assigned 
192.168.64.3 via lease from the UTM virtual router. Confirmed subnet 
(255.255.255.0) and default gateway (192.168.64.1) for the static 
configuration.

## Resolution
Attempted to apply static IP via GUI — change did not apply. Used 
Administrator Command Prompt to force the configuration via netsh:

netsh interface ip set address "Ethernet" static 192.168.64.10 255.255.255.0 192.168.64.1
netsh interface ip set dns "Ethernet" static 8.8.8.8

Ran ipconfig /all to verify — confirmed DHCP Enabled: No and IPv4 
Address: 192.168.64.10. Ran ping 8.8.8.8 — 0% packet loss confirmed 
internet connectivity maintained on new static IP.

## Evidence
- Screenshot: 03_ipconfig_before_static.png
- Screenshot: 05_ipconfig_after_static.png
- Screenshot: 04_netsh_static_ip_command.png
- Screenshot: 06_ping_verified_static.png
