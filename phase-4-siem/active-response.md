# Active Response: Automated Brute-Force Reaction

## Objective
Extend the custom brute-force detection rule (100010) with an
automated Active Response, so the SIEM doesn't just detect the
attack but reacts to it - moving from detection to response,
a core SOC capability.

## Available Response Scripts
Wazuh ships a small set of built-in Active Response scripts.
Checked what's available on the Windows agent:

    Get-ChildItem "C:\Program Files (x86)\ossec-agent\active-response\bin"

Result: netsh.exe, restart-wazuh.exe, route-null.exe.
**File:** phase-4-siem-16-active-response-scripts-available.png

## Configuration
The manager's ossec.conf already had command definitions for
netsh (Windows firewall blocking) and a commented-out
active-response placeholder ready to fill in:

    <!--
    <active-response>
      active-response options here
    </active-response>
    -->
**File:** phase-4-siem-17-active-response-placeholder-comment.png

Also noted an existing global whitelist, preventing Active
Response from ever acting against loopback/localhost:

    <global>
      <white_list>127.0.0.1</white_list>
      <white_list>^localhost.localdomain$</white_list>
    </global>
**File:** phase-4-siem-18-active-response-whitelist-config.png

Configured netsh to trigger on rule 100010:

    <active-response>
      <command>netsh</command>
      <location>local</location>
      <rules_id>100010</rules_id>
      <timeout>300</timeout>
    </active-response>

## Test 1: Local Console Brute Force
Repeated the earlier brute-force test (5+ failed logins) to
trigger rule 100010 again and observe Active Response.
**File:** phase-4-siem-19-brute-force-alert-second-test.png

The active-responses.log on the agent showed the full lifecycle:
1. `command: add` - the manager dispatched the block
2. `command: check_keys` - netsh checked whether 127.0.0.1 was
   already blocked
3. `command: continue` - proceeded with the block attempt
4. Exactly 300 seconds later (matching the configured timeout),
   `command: delete` - the block was automatically reversed

No firewall rule persisted (confirmed via
`netsh advfirewall firewall show rule name=all`), and the
brute-force test's source IP was recorded as 127.0.0.1, since
the login attempts were made directly at the VM's console
rather than over the network. This is consistent with the
manager's whitelist configuration, and/or Windows Firewall not
applying rules to loopback traffic in the way it does to real
network interfaces.

## Test 2: disable-account (Attempted)
Also configured a second active-response using `disable-account`,
intending to lock the targeted user account regardless of source
IP (sidestepping the loopback issue entirely). Triggered the
brute-force rule again, but the agent log showed:

    Active response command not present:
    'active-response/bin/disable-account'. Not using it on this
    system.

This confirmed disable-account is a Linux-only Active Response
script in Wazuh (built around Linux user-management commands)
and is not shipped for Windows agents - consistent with the
earlier Get-ChildItem listing, which only showed netsh.exe,
restart-wazuh.exe, and route-null.exe.

## Key Takeaways
- Active Response configuration and dispatch worked correctly
  end-to-end: the manager identified the trigger, and the
  full add/check/continue/delete lifecycle executed exactly as
  configured, including the automatic timeout-based reversal.
- Testing IP-based blocking meaningfully requires a genuine
  non-loopback source IP. A local console-based brute force test
  (as used throughout this project for practicality) generates
  127.0.0.1 as the source, which Wazuh's built-in whitelist
  correctly protects from being blocked - by design, to prevent
  a SIEM from ever locking out its own management interface.
- Not all Active Response scripts are cross-platform - commands
  like disable-account are Linux-specific and will silently do
  nothing on a Windows agent, without necessarily causing an
  obvious downstream error other than a log entry to explain why.
- For a genuinely conclusive real-world test of IP-based Active
  Response, a remote (non-loopback) brute-force attempt - e.g.
  from another device on the network, or via port-forwarded
  RDP - would be required. This was identified as a valid next
  step but not completed in this session, in favour of
  documenting the mechanism and limitations discovered.
