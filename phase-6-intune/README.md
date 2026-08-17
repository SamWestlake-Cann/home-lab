# Phase 6: Microsoft Intune — Device Management

**Status: Planned, not yet started**

This phase extends the Westlake IT Solutions identity governance work
from Phase 5d by adding device management, closing the gap left by
CA002 (Require compliant device for IT Admins), which has never been
enforceable against a real compliant device.

## Planned scope

- Activate Microsoft Intune Plan 1 free trial
- Enroll the existing UTM Windows 11 VM (SAM-LAB-CLIENT01) built in
  earlier phases
- Build a Compliance Policy (e.g. require BitLocker, passcode, minimum
  OS version)
- Confirm the device is correctly marked Compliant / Non-compliant
- Return to CA002 in Phase 5d and verify it finally evaluates correctly
  against a real compliant device
- Optionally: Configuration Profiles, App Protection Policies

## Why this phase

Real organisations secure identity first, then device, then data.
Phase 5 covered identity governance in full (users, groups,
Conditional Access, PIM, Access Reviews). This phase is the natural
next step in that same maturity progression, and directly completes
unfinished work from Phase 5d rather than starting something
unrelated.

## Contents

- `01-scenario-design.md` — planning notes
- `enrollment.md` — device enrollment steps and screenshots
- `compliance-policies.md` — compliance policy configuration
- `configuration-profiles.md` — configuration profile work (if done)
- `screenshots/` — numbered screenshots continuing the home lab
  sequence
