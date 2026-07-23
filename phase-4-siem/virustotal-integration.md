# VirusTotal Integration and Malware Detection Testing

## Objective
Integrate Wazuh with VirusTotal for automated threat intelligence
enrichment of File Integrity Monitoring (FIM) alerts, then test
end-to-end malware detection using a safe, industry-standard
test file (EICAR).

## Configuration
Signed up for a free VirusTotal account and generated a personal
API key (standard free tier: 4 lookups/min, 500/day, 15.5K/month -
not for commercial use, sufficient for lab testing).

Added an integration block to the Wazuh manager's ossec.conf,
tying VirusTotal lookups to the syscheck (FIM) alert group:

    <integration>
      <name>virustotal</name>
      <api_key>[REDACTED]</api_key>
      <group>syscheck</group>
      <alert_format>json</alert_format>
    </integration>

## Test 1: Clean File
Created a plain text file in a monitored real-time directory
(Startup folder) to validate the integration end-to-end.

1. FIM detected the new file: rule 554, "File added to the
   system", level 5.
   **File:** phase-4-siem-20-virustotal-fim-trigger.png
2. Wazuh automatically queried VirusTotal with the file's hash
   and received a result: rule 87103, "VirusTotal: Alert - No
   records in VirusTotal database", level 3 - correctly
   reporting the file as unknown/unseen, since it was a harmless
   test file with no prior history.
   **File:** phase-4-siem-21-virustotal-lookup-result.png

This confirmed the full pipeline: file change -> FIM detection
-> hash sent to VirusTotal API -> response received and
classified by Wazuh.

## Test 2: EICAR Test File
To validate detection of an actual flagged file (without
introducing real malware), created the EICAR test string - a
standardised, harmless file recognised by antivirus engines
specifically for testing purposes.

The file initially appeared to persist (Test-Path returned True),
but attempts to access it via FIM failed:

    wazuh-agent: ERROR: (6716): Could not open handle for
    '...eicar-test.txt'. Error code: 225
    wazuh-agent: WARNING: CreateFile(): Operation did not
    complete successfully because the file contains a virus or
    potentially unwanted software. (225)
**File:** phase-4-siem-22-eicar-defender-blocked.png

## Investigating the Block
Checked Windows Defender's own detection history directly:

    Get-MpThreatDetection

Result: two separate detections logged, at 10:24 (via
sihost.exe, the original file write) and 10:31 (via
wazuh-agent.exe, Wazuh's own FIM scan attempting to read the
file) - both remediated by 10:36. This explained the FIM error:
Defender had already locked/quarantined the file, preventing
Wazuh from ever completing a hash check on it.
**File:** phase-4-siem-23-defender-threat-detection-detail.png

## Root Cause: Missing Log Channel
Checked whether Wazuh was even collecting Windows Defender's own
detection events. Defender logs to a dedicated channel,
Microsoft-Windows-Windows Defender/Operational, separate from
the generic Application log already being collected - the same
pattern encountered previously with Task Scheduler and
PowerShell channels.

Confirmed the channel was enabled at the OS level
(`wevtutil gl "Microsoft-Windows-Windows Defender/Operational"`)
but not yet configured in Wazuh's agent. Added the missing
<localfile> block, restarted the agent, and re-ran the EICAR
test with a fresh file.

## Result: High-Severity Detection
This time, Wazuh correctly captured Defender's detection
directly:

    Rule 62123: "Windows Defender: Antimalware platform detected
    potentially unwanted software", Level 12

Level 12 is the highest severity seen across this entire
project - correctly reflecting a genuine, real-time malware
detection rather than a lower-confidence hash lookup.
**File:** phase-4-siem-24-defender-malware-alert-level12.png

## Key Takeaways
- VirusTotal integration works correctly for hash-based lookups
  triggered by FIM, and correctly distinguishes "no record"
  results from flagged ones.
- Testing malware detection safely is possible using the EICAR
  standard test file, without introducing any real risk.
- Windows Defender's real-time protection can interfere with
  FIM's ability to read a file it considers malicious - a subtle
  but important interaction between antivirus and SIEM tooling,
  since the file being "protected" (locked) is exactly what
  prevented the intended FIM/VirusTotal chain from completing.
- As with Task Scheduler and PowerShell before, an important
  Windows event channel (Defender's own Operational log) was not
  collected by Wazuh's default configuration and needed to be
  explicitly added - a recurring theme throughout this project
  worth remembering when configuring any new Wazuh deployment.
- The most reliable detection in this test came directly from
  Windows Defender's own real-time engine (level 12), rather
  than a downstream VirusTotal hash lookup - a good illustration
  of layered defense, where multiple detection sources
  (antivirus + SIEM + threat intel) each contribute differently
  to overall visibility.
