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

## Approval workflow added

Configured a second layer of control on the User Administrator
eligible role, requiring approval before activation rather than
allowing self-service elevation.

- **Approver:** Amy Churm (Security Analyst, Cybersecurity)
- **Activation requirements:** Azure MFA, justification required,
  8-hour maximum duration, approval required

**Design rationale:** rather than using an existing admin as approver,
a Security Analyst was chosen deliberately to model a real-world
separation-of-duties pattern common in MSPs and SOC-integrated
organisations — the person requesting elevated access (IT Operations)
is not the same function approving it (Security). This mirrors how
many real environments route privileged access requests through
security/SOC review rather than peer IT admins.

This closes the gap from the original PIM configuration, where Sam
could self-activate User Administrator with no oversight. Future
activation attempts will now generate an approval request that Amy
must review and approve before the role becomes active.

## Approval workflow tested end-to-end

Verified the full two-person PIM activation flow, live, using genuine
separate user sessions:

1. Signed in as Sam Westlake-Cann, submitted an activation request for
   User Administrator (justification + duration, same as the original
   self-activation test)
2. Request entered a **pending approval** state rather than activating
   immediately — confirming the approval requirement is genuinely
   enforced, not just configured
3. Signed in as Amy Churm, located the request under **Approve
   requests**, reviewed it, and approved with a comment
4. Confirmed the role became Active on Sam's side following approval

This demonstrates a real separation-of-duties control: privileged
access elevation now requires justification, MFA, a bounded duration,
AND a second person's sign-off — not just a single admin self-serving
elevated access.

Screenshots: 68 (pending request in Amy's approval queue), 69 (Amy
approving with comment)
