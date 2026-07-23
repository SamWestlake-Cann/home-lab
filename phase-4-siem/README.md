# Phase 4: SIEM Deployment (Wazuh)

## Objective
Deploy a self-hosted SIEM to monitor the existing home lab
Windows VM (SAM-LAB-CLIENT01), gaining hands-on experience with
log collection, agent-based monitoring, and the core toolset
used in SOC/cybersecurity analyst roles.

## Approach
Originally planned as a cloud-hosted deployment (Oracle Cloud
Free Tier), but pivoted to a local Docker deployment after
repeated "out of capacity" errors for Ampere A1.Flex instances
in the Sydney region - a known limitation of Oracle's Always
Free tier in smaller regions. Running Wazuh via Docker locally
avoided this entirely and required minimal disk space (~5-10GB)
given ample free space on the host Mac (290GB available).

## Setup
- **SIEM stack:** Wazuh 4.9.0 (manager, indexer, dashboard)
  via official Docker Compose single-node deployment
- **Host:** MacBook Air (Apple Silicon), Docker Desktop
- **Monitored endpoint:** SAM-LAB-CLIENT01 (Windows 11 VM,
  existing home lab client)
- **Agent-to-manager communication:** confirmed over local
  network (192.168.1.114, permanently reserved via router
  DHCP reservation), agent registered and status Active

## Steps completed
1. Installed Docker Desktop
2. Cloned official Wazuh Docker repository (v4.9.0)
3. Generated indexer/dashboard/filebeat certificates
4. Started full stack via docker compose up -d
5. Logged into Wazuh dashboard (localhost:443)
6. Deployed Wazuh agent (MSI) to SAM-LAB-CLIENT01
7. Resolved initial "duplicate agent" connection issue
   (agent auto-recovered and connected successfully)
8. Confirmed agent status: Active
9. Diagnosed and resolved an agent disconnection caused by
   the host Mac's IP changing via DHCP - see
   network-troubleshooting.md for full details, including an
   attempted mDNS hostname fix and the permanent resolution
   via a router-level DHCP reservation
10. Wrote and validated a custom detection rule for brute-force
    login attempts, and tested detection coverage across four
    additional attacker techniques (FIM, scheduled task
    persistence, PowerShell script block logging, audit log
    clearing) - see detection-testing.md for full details
11. Configured and tested Active Response (automated reaction to
    the custom brute-force rule), validating the full
    add/check/continue/delete lifecycle and discovering two
    genuine platform limitations (loopback whitelisting,
    Windows/Linux script availability) - see active-response.md
    for full details
12. Integrated VirusTotal for automated threat intelligence
    enrichment of FIM alerts, tested with both a clean file and
    the EICAR standard test file, discovered and resolved a
    missing Windows Defender log channel, and confirmed
    detection at the highest severity level (12) seen in this
    project - see virustotal-integration.md for full details

## Screenshots
See numbered screenshots in this folder (01, 03-24) documenting
each stage: initial deployment, agent connection, the network
troubleshooting described in network-troubleshooting.md, the
detection testing described in detection-testing.md, the Active
Response work described in active-response.md, and the
VirusTotal integration described in virustotal-integration.md.

## Next steps
- Test Active Response IP blocking against a genuine remote
  (non-loopback) brute-force attempt, e.g. via port-forwarded
  RDP from another device on the network
- Explore Wazuh's Vulnerability Detection module
- Write a formal Incident Response report packaging the
  brute-force detection work
- Consider adding a second monitored endpoint for correlation
  across multiple agents
