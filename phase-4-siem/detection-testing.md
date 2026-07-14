# Detection Testing: Custom Rules and Multi-Technique Validation

## Objective
Validate the Wazuh SIEM's detection capabilities against a range
of realistic attacker techniques, going beyond basic agent
connectivity to prove the pipeline actually detects and correctly
classifies malicious/suspicious behaviour.

## Test 1: Brute Force Detection (Custom Rule)

### Initial finding
Repeated Windows logon failures (event 4625) were logged
individually by Wazuh's default ruleset (rule 60122, level 5)
but were not correlated into a single higher-severity alert.
Windows' own account lockout mechanism did fire a distinct,
correctly MITRE-mapped alert (rule 60115, level 9, T1110/T1531)
once the account actually locked - but only after the lockout
itself, not during the attack in progress.

### Custom rule written
Added a correlation rule to /var/ossec/etc/rules/local_rules.xml
inside the Wazuh manager container, firing when 5+ logon failures
(rule 60122) occur within a 120 second window for the same agent:

    <group name="local,windows,authentication_failed,">
      <rule id="100010" level="10" frequency="5" timeframe="120">
        <if_matched_sid>60122</if_matched_sid>
        <description>Possible brute force attack: 5 or more Windows
        logon failures in 2 minutes.</description>
        <mitre>
          <id>T1110</id>
        </mitre>
        <group>authentication_failures,</group>
      </rule>
    </group>

### Result
Rule fired correctly and earlier than Windows' own lockout event -
catching the pattern in progress rather than only after the account
was already locked. Verified with a live test (5 deliberate wrong
password attempts), confirmed in Wazuh dashboard as rule 100010,
level 10, T1110 (Credential Access).
**Files:** phase-4-siem-08b-logon-failures-4625-view.png,
phase-4-siem-09-brute-force-lockout-alert.png,
phase-4-siem-10-custom-rule-brute-force-fired.png

## Test 2: File Integrity Monitoring (FIM)

Edited the Windows hosts file (%WINDIR%\System32\drivers\etc\hosts)
to test FIM. No alert fired for this specific file, since that
exact path is not included in Wazuh's default monitored
directories list (only %WINDIR% itself and specific tool
executables are covered by default). Forcing a syscheck rescan
did correctly detect and alert on several genuine registry key/
value changes (rules 550, 594, 750) that occurred incidentally
during the agent restart - confirming FIM is actively working,
just not for that specific untracked file path.
**File:** phase-4-siem-11-fim-registry-changes.png

## Test 3: Scheduled Task Persistence

Created a scheduled task via schtasks.exe to simulate a common
persistence technique. Initial attempt produced no alert, because
the Microsoft-Windows-TaskScheduler/Operational event log channel
is disabled by default in Windows - separate from whether Wazuh
is configured to read it. Enabled the channel with wevtutil,
added a matching <localfile> block to the agent's ossec.conf, and
created a fresh task. Correctly detected as rule 67014,
"Task Scheduler: .", level 3, MITRE T1053.005 (Scheduled Task/Job).

Note: this event (Event ID 106, Task Registered) is a different
Windows logging mechanism from the Security log's own scheduled
task audit event (Event ID 4698), which requires separate Advanced
Audit Policy configuration.
**Files:** phase-4-siem-12-scheduled-task-persistence.png,
phase-4-siem-13-rule-details-task-scheduler.png

## Test 4: PowerShell Script Block Logging

Ran Invoke-WebRequest against a test URL to check PowerShell
command-level visibility. No alert fired initially, since
PowerShell Script Block Logging is disabled by default. Enabled
it via registry (HKLM:\Software\Policies\Microsoft\Windows\
PowerShell\ScriptBlockLogging), added a matching <localfile>
block for the Microsoft-Windows-PowerShell/Operational channel,
restarted the agent, and re-ran the command. Correctly detected
as rule 91816, "Powershell script querying system environment
variables", level 4, MITRE T1082 (System Information Discovery) -
demonstrating Wazuh inspects actual script block content, not
just the fact that PowerShell ran.
**File:** phase-4-siem-14-powershell-scriptblock-logging.png

## Test 5: Audit Log Clearing (Anti-Forensics)

Ran wevtutil cl Security to clear the Windows Security event log,
simulating a common attacker technique to cover their tracks after
an intrusion. Correctly and immediately detected as rule 63103,
"The audit log was cleared", level 5, MITRE T1070 (Indicator
Removal, Defense Evasion) - Windows deliberately logs this
specific action to the log itself as it is cleared, ensuring the
action cannot go undetected even though the log's other contents
are wiped.
**File:** phase-4-siem-15-audit-log-cleared.png

## Key Takeaways
- Wazuh's default ruleset does not correlate raw event patterns
  (like repeated logon failures) automatically - custom rules
  using frequency/timeframe/if_matched_sid are needed to detect
  attacks in progress rather than only after a downstream effect
  (like Windows' own account lockout) occurs.
- Several important Windows event log channels (Task Scheduler
  Operational, PowerShell Operational) are disabled by default
  and must be explicitly enabled at the OS level, separate from
  configuring Wazuh's agent to read them.
- Different Windows event IDs can represent the same conceptual
  action via different logging mechanisms (e.g. Task Scheduler's
  own log vs. the Security log's audit trail for the same action) -
  understanding which one is actually configured and monitored
  matters for accurate detection coverage.
- Across five distinct techniques spanning Credential Access,
  Persistence, Discovery, and Defense Evasion MITRE tactics, the
  SIEM pipeline (Windows event -> Wazuh agent -> manager -> rule
  engine -> indexer -> dashboard) performed correctly once properly
  configured, validating the full detection pipeline end-to-end.
