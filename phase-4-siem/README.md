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
in the Sydney region — a known limitation of Oracle's Always 
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
  network (192.168.1.106), agent registered and status Active

## Steps completed
1. Installed Docker Desktop
2. Cloned official Wazuh Docker repository (v4.9.0)
3. Generated indexer/dashboard/filebeat certificates
4. Started full stack via `docker compose up -d`
5. Logged into Wazuh dashboard (localhost:443)
6. Deployed Wazuh agent (MSI) to SAM-LAB-CLIENT01
7. Resolved initial "duplicate agent" connection issue 
   (agent auto-recovered and connected successfully)
8. Confirmed agent status: Active

## Screenshots
See numbered screenshots in this folder (01-05) documenting 
each stage from containers running through to active agent 
connection.

## Next steps
- Generate test security events (failed logons, file changes) 
  to validate alerting pipeline
- Explore File Integrity Monitoring module
- Write/trigger a custom detection rule
