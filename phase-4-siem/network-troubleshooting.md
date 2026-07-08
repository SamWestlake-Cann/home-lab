# Network Troubleshooting: Agent Disconnection

## Issue
Wazuh agent (SAM-LAB-CLIENT01) showed status "Disconnected"
after ~2 days of uptime, despite the agent service running.

## Diagnosis
Checked agent log - showed repeated timeout errors connecting
to 192.168.1.106:1514. Checked Mac's current IP - had changed
to 192.168.1.114 (DHCP lease renewal), meaning the agent was
trying to reach an address that no longer existed.

## Attempted Fix 1: mDNS Hostname
Tried using sams-MacBook-Air.local instead of a hardcoded IP,
to make the config resilient to future IP changes. Test-NetConnection
from the Windows VM succeeded initially, but Wazuh agent
rejected it: "All server addresses are IPv6 link-local and no
interface to any server block has been configured" - mDNS
resolved to an IPv6 link-local address which Wazuh doesn't
support without extra interface config.

## Fix: DHCP Reservation (Router-level)
Reverted agent config to a plain IPv4 address, then fixed the
root cause at the network level instead: added a DHCP
Address Reservation on the router (TP-Link Archer VR400)
binding the Mac's MAC address permanently to 192.168.1.114.

Verified with:

    sudo ipconfig set en0 DHCP
    ifconfig en0 | grep "inet "
    -> inet 192.168.1.114

Confirmed the Mac retains the same IP after a forced DHCP
renewal - this IP will no longer change, permanently resolving
future agent disconnection issues caused by IP drift.

## Resolution Confirmed
After applying the DHCP reservation and updating the agent
config back to a plain IPv4 address (192.168.1.114), the agent
reconnected successfully:

    2026/07/08 11:27:57 wazuh-agent: INFO: (4102): Connected to the
    server ([192.168.1.114]:1514/tcp).
    2026/07/08 11:28:02 wazuh-agent: INFO: Agent is now online.
    Process unlocked, continuing...

Rootcheck, FIM real-time monitoring, and SCA scan all resumed
normally. Confirmed Active status on the Wazuh dashboard
(Server Management -> Endpoints Summary).

## Lessons Learned
- Docker containers persisting across reboots doesn't guarantee
  the agent stays connected - network-layer changes (DHCP)
  can silently break connectivity even when every service is
  technically "running."
- mDNS (.local) hostnames aren't a safe drop-in replacement for
  IPs in this setup, since Wazuh's agent doesn't handle IPv6
  link-local resolution without additional config - a good
  example of a "clean" fix introducing a new, different failure
  mode.
- The most robust fix was addressing the root cause at the
  network layer (DHCP reservation) rather than patching the
  application config repeatedly.
