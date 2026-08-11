# Privileged Identity Management (PIM) — Westlake IT Solutions

Configured and tested a full PIM eligible-to-active cycle for the
User Administrator role, demonstrating just-in-time privileged access
rather than standing admin rights — the same model used by real MSPs
and security-conscious organisations.

## Eligible assignment
- **Role:** User Administrator
- **Scope:** Directory (Westlake Cann tenant)
- **Member:** Sam Westlake-Cann (Samuel.WC@WestlakeCann.onmicrosoft.com)
  — the fictional IT Operations user, deliberately kept separate from
  the real tenant global admin account so the activation flow could be
  tested as a genuine standard user rather than an already-standing
  admin.
- **Assignment type:** Eligible (not Active)
- **Duration:** Permanently eligible

## Activation test
Signed in as Sam Westlake-Cann in a private browser session and
completed a live activation:
- **Duration requested:** 8 hours
- **Justification:** "Performing scheduled user account maintenance for
  IT Operations"
- **Result:** Role successfully moved from Eligible to Active, with a
  visible countdown expiry (12/08/2026, 5:00:44 pm) and a Deactivate
  option available to end the elevation early.

## Why this matters
Standing admin access is a common attack surface — if an account is
compromised, an attacker inherits whatever privileges that account
holds at all times. PIM eliminates this by keeping privileged roles
inactive by default, requiring active justification and time-bound
activation before elevated permissions apply. This mirrors how MSPs
manage admin access for technicians who only need elevated rights
intermittently.

## Notes
- Screenshots: 52 (eligible assignment, Sam's view), 53 (activation
  request form), 54 (activation confirmation banner), 55 (active
  assignment with countdown/deactivate option)
- Next step for a future session: configure PIM approval workflows
  (requiring a second admin to approve activation) and PIM audit
  history review.
