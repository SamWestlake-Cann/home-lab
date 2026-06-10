# Phase 1 — Networking Fundamentals

## Objective
Map and understand the home lab network architecture, run key diagnostic 
commands on both host and VM, analyse traffic behaviour, and configure 
a static IP on the Windows 11 VM.

## Environment
- **Host:** MacBook Air (192.168.1.106) on home network 192.168.1.0/24
- **VM:** Windows 11 — SAM-LAB-CLIENT01 (192.168.64.10 static)
- **Network mode:** UTM Shared Network (NAT)
- **Virtual router:** 192.168.64.1 (UTM bridge100 interface)

## Tasks Completed

### 1. Network Topology Diagram
Mapped the full network architecture from internet down to the VM, 
identifying both the physical home network and the isolated virtual 
subnet created by UTM. Confirmed NAT mode by comparing IP ranges 
on both interfaces.

### 2. VM Network Mode Identified
Confirmed UTM Shared Network (NAT) mode. The VM sits on a separate 
virtual subnet (192.168.64.0/24) and cannot be reached directly from 
the home network — only outbound traffic is permitted through the 
UTM virtual router.

### 3. Network Commands — Mac & Windows
Ran and documented key diagnostic commands on both machines.

**Mac:**
- `ifconfig` — identified all network interfaces including en0 (WiFi) 
and bridge100 (UTM virtual bridge)
- `ping 192.168.64.10` — confirmed NAT blocks inbound to VM (100% loss)
- `arp -a` — mapped all known devices across both network interfaces

**Windows VM:**
- `ipconfig /all` — documented full network configuration
- `ping 8.8.8.8` — confirmed outbound internet via NAT (0% loss)
- `ping 192.168.1.106` — confirmed VM can reach Mac host (0% loss)
- `nslookup google.com` — confirmed DNS resolution working

### 4. NAT Behaviour Proven
| Test | From | To | Result | Reason |
|---|---|---|---|---|
| ping | Mac | VM | ❌ Timeout | NAT blocks inbound |
| ping | VM | Mac | ✅ Success | Host always reachable |
| ping | VM | 8.8.8.8 | ✅ Success | NAT allows outbound |
| nslookup | VM | google.com | ✅ Success | DNS through NAT |

### 5. ARP Table Analysis
Ran `arp -a` on Mac host. Confirmed two completely isolated interfaces:
- `en0` — physical WiFi, home network devices only
- `bridge100` — UTM virtual bridge, VM and virtual router only

### 6. Static IP Configuration
Changed Windows 11 VM from DHCP to static IP.

| Setting | Before | After |
|---|---|---|
| DHCP Enabled | Yes | No |
| IPv4 Address | 192.168.64.3 | 192.168.64.10 |
| DNS Server | Auto (192.168.64.1) | 8.8.8.8 |

GUI change did not apply initially — used `netsh` via Administrator 
Command Prompt to force the change:

Verified with `ipconfig /all` and `ping 8.8.8.8` — 0% packet loss 
confirmed internet working on new static IP.

## Key Learnings
- NAT blocks unsolicited inbound traffic by design — a "failed" ping 
is not always a fault
- The GUI doesn't always apply changes immediately — knowing the 
command line alternative is essential
- ARP tables reveal network interface separation that confirms 
theoretical topology diagrams
- Static IPs are critical for lab machines so monitoring tools and 
remote access always know where to find them

## Screenshots
All screenshots are in this folder, numbered in sequence.
