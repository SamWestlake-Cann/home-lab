# CIS Benchmark Hardening

## Objective
Apply security hardening settings from the CIS Windows 11
benchmark to the lab VM (SAM-LAB-CLIENT01), reducing attack
surface across legacy protocols, logging visibility, privilege
escalation prompts, removable media, and name resolution.

## Settings Applied

### 1. Disable SMBv1
Legacy SMB protocol vulnerable to well-known exploits (e.g.
EternalBlue). Disabled via:
`Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol -NoRestart`
Result: disabled successfully, no restart required.
**File:** 11_cis_disable_smbv1.png

### 2. Enable Firewall Logging
Enabled logging of both blocked and allowed connections across
all three firewall profiles (Domain, Public, Private) for
visibility into network activity.

**Troubleshooting:** initial attempt combined all three profiles
in a single `Set-NetFirewallProfile` command, which failed with
"the parameter is incorrect" (Windows System Error 87). Resolved
by running the command separately for each profile instead.
**Files:** 12_cis_firewall_logging_error.png,
13_cis_firewall_logging_success.png,
14_cis_firewall_logging_verified.png

### 3. Enable UAC (User Account Control) Prompt
Set `ConsentPromptBehaviorAdmin` to 2 (prompt for consent on
the secure desktop), ensuring admin actions require explicit
approval rather than running silently.
**File:** 15_cis_uac_verified.png

### 4. Disable AutoPlay
Set `NoDriveTypeAutoRun` to 255 (disable AutoPlay on all drive
types), reducing risk from malicious removable media.

**Troubleshooting:** initial attempt failed because the target
registry path
(`HKLM:\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer`)
didn't exist yet. Resolved by creating the path first with
`New-Item -Force`, then applying the setting.
**File:** 16_cis_autoplay_complete.png

### 5. Disable LLMNR (Link-Local Multicast Name Resolution)
Set `EnableMulticast` to 0 under the DNS Client policy key,
disabling LLMNR to reduce exposure to LLMNR poisoning/spoofing
attacks, a common lateral movement technique.
**File:** 17_cis_llmnr_verified.png

## Why This Matters
Each setting maps to a real-world attack technique commonly
seen in both red team exercises and actual incidents: SMBv1
(EternalBlue-style exploits), firewall log blindness (missed
detection), UAC bypass (silent privilege escalation), AutoPlay
(malicious USB payloads), and LLMNR poisoning (credential
theft via Responder-style tools). Hardening these settings is
a direct, practical link between theory and the kind of
baseline configuration checks a SOC/security analyst would
expect to see — or flag as missing — in a real environment.
