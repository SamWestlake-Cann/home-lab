# Phase 5d: Entra ID Advanced — Scenario Design

## Overview
This phase simulates identity governance design for a fictional 40-person MSP,
**Westlake IT Solutions**, built in a real Entra ID tenant
(`WestlakeCann.onmicrosoft.com`). The goal is to practise Conditional Access,
Privileged Identity Management (PIM), and Dynamic Groups against a realistic
department structure rather than generic test accounts.

## Fictional company: Westlake IT Solutions
A Sydney-based MSP, structured with departments that map to distinct
identity/access requirements.

## Departments

| Department | Purpose in the lab |
|---|---|
| IT Operations | Admin-heavy — PIM eligible roles live here |
| Service Desk (L1/L2) | Standard users — CA baseline policies |
| Cybersecurity/SOC | Sensitive access — stricter CA (compliant device) |
| Sales & Client Services | External-facing — CA for risky sign-ins |
| Finance | High-value target — MFA enforcement, location controls |
| Contractors/External | Guest access — time-bound, CA-restricted |

## Users (Phase 5d, round 1)

| Name | UPN | Department | Job Title |
|---|---|---|---|
| Sam Westlake-Cann | Samuel.WC@WestlakeCann.onmicrosoft.com | IT Operations | Systems Administrator |
| Stephanie Westlake | Stephanie.W@WestlakeCann.onmicrosoft.com | Service Desk | L1 Support Technician |
| Amy Churm | Amy.C@WestlakeCann.onmicrosoft.com | Cybersecurity | Security Analyst |
| Neil Cutler | Neil.C@WestlakeCann.onmicrosoft.com | Finance | Finance Manager |
| Tiffany Cutler | Tiffany.C@WestlakeCann.onmicrosoft.com | Sales & Client Services | Account Manager |

All users created as Members, Usage location = Australia.

**Note:** the real tenant global admin account
(`Sam@WestlakeCann.onmicrosoft.com`) is kept separate and acts as the
"practice admin" performing the configuration — it is not part of the
fictional org chart, so that PIM activation flows can be tested realistically
on a standard user rather than an already-standing admin.

## Planned Dynamic Groups
- `SOC-All` → `user.department -eq "Cybersecurity"`
- `IT-Admins-Eligible` → `user.jobTitle -contains "Administrator"`
- `Finance-Sensitive` → `user.department -eq "Finance"`

## Planned Conditional Access policies
- CA001: Require MFA — all users
- CA002: Require compliant device — IT-Admins-Eligible
- CA003: Block legacy authentication — org-wide
- CA004: Require MFA every sign-in — Contractors/External

## Planned PIM eligible roles
- User Administrator → Sam Westlake-Cann (IT Ops)
- Security Reader → Amy Churm (SOC)

## Growth plan
Additional users/departments will be added in later sessions to simulate
business growth and test how Dynamic Groups and CA policies scale.

## Growth round 2 — expanded roster

Added 9 additional users to simulate business growth, bringing total
headcount to 14 (plus the real tenant admin account).

| Name | Department | Job Title |
|---|---|---|
| Sam Reilly | IT Operations | Helpdesk Team Lead |
| Priya Anand | Service Desk | L2 Support Technician |
| Marcus Webb | Cybersecurity | SOC Analyst |
| Elena Petrova | Cybersecurity | Security Engineer |
| David Cutler | Finance | Accounts Payable Officer |
| Rachel Cutler | Finance | Payroll Administrator |
| Liam Foster | Sales & Client Services | Account Manager |
| Grace Foster | Sales & Client Services | Business Development Rep |

All created as Members, Usage location = Australia.

A Guest/External contractor account is planned but not yet created —
requires a real external email address for the invite flow, to be
added in a future session (needed to properly test CA004).

### Dynamic group re-verification after growth

- **SOC-ALL** — now includes Amy Churm, Marcus Webb, Elena Petrova
- **Finance-Sensitive** — now includes Neil Cutler, David Cutler,
  Rachel Cutler
- **IT-Admins-Eligible** — now includes Sam Westlake-Cann AND
  **Rachel Cutler**. This was an unintended but instructive result:
  Rachel's job title "Payroll Administrator" contains the word
  "Administrator", so the `jobTitle -contains "Administrator"` rule
  correctly matched her even though she sits in Finance, not IT.
  This demonstrates the rule is doing literal attribute matching
  rather than "is this person in IT" — a useful real-world lesson in
  dynamic group rule precision. A tighter rule (e.g. combining job
  title AND department) would be needed in production to scope this
  group correctly.

Screenshots: 56 (full roster), 57 (IT-Admins-Eligible updated),
58 (Finance-Sensitive updated), 59 (SOC-ALL updated)

## Guest/External contractor account

Invited an external Guest user ("John Smith") to represent the
Contractors/External tier, using **Users → Invite external user**.

- **Job title:** IT Support Contractor
- **Department:** Contractor/External
- **User type:** Guest (confirmed)
- **Invitation status:** Pending acceptance

Verified via **Entra ID → Audit Logs** that the invitation was
successfully processed on the Entra ID side (Invited Users |
UserManagement | Invite external user | **Success**). The invitation
email was not received at the target external address (tried both a
Hotmail and a Gmail address, checked inbox/spam/promotions on both) —
this appears to be a mail delivery/filtering issue outside the
tenant's control, not a configuration fault, since Entra ID's own
audit trail confirms the invite was sent successfully.

The Guest account itself is fully usable for demonstrating dynamic
targeting (e.g. CA004 targets "Guest or external users" by user type,
which does not require invitation acceptance to apply). Full sign-in
testing as this guest is deferred to a future session, potentially
using a different email provider.

## Emergency access (break-glass) account

Following a Microsoft Entra Identity Secure Score recommendation
("Designate more than one global admin"), created a dedicated
emergency access account rather than promoting an existing fictional
user, per Microsoft's documented best practice.

- **User:** Emergency Access Admin
  (breakglass-admin@WestlakeCann.onmicrosoft.com)
- **Role:** Global Administrator, assigned as a **standing/active**
  role rather than PIM-eligible — deliberate choice, since a break-glass
  account must work instantly during an emergency without requiring an
  activation step.
- **Conditional Access:** Excluded from all 4 CA policies (CA001-CA004),
  same as the primary admin account, to guarantee this account can
  never be locked out regardless of policy misconfiguration.
- **Purpose:** Recovery path if the primary Global Admin account
  (Sam@WestlakeCann.onmicrosoft.com) is ever compromised, locked out,
  or has MFA/device issues.

This addressed a real Identity Secure Score finding for the tenant,
demonstrating response to Microsoft's own security recommendations
rather than just self-directed lab design.

## User consent restriction (Identity Secure Score recommendation)

Addressed a second Identity Secure Score finding ("Do not allow users
to grant consent to unreliable applications") by configuring:

**Entra ID → Enterprise applications → Consent and permissions →
User consent settings**

Selected: **"Allow user consent for apps from verified publishers,
for selected permissions"** — Microsoft's recommended middle ground.
Users can still consent to low-impact permissions from verified
publishers or apps registered within the organisation, but cannot
grant broader or unverified app access without admin review. This
mitigates OAuth consent phishing risk (malicious apps tricking users
into granting mailbox/file access) while avoiding the operational
overhead of requiring admin approval for every legitimate app request.

## User consent restriction (Identity Secure Score recommendation)

Addressed a second Identity Secure Score finding ("Do not allow users
to grant consent to unreliable applications") by configuring:

**Entra ID → Enterprise applications → Consent and permissions →
User consent settings**

Selected: **"Allow user consent for apps from verified publishers,
for selected permissions"** — Microsoft's recommended middle ground.
Users can still consent to low-impact permissions from verified
publishers or apps registered within the organisation, but cannot
grant broader or unverified app access without admin review. This
mitigates OAuth consent phishing risk (malicious apps tricking users
into granting mailbox/file access) while avoiding the operational
overhead of requiring admin approval for every legitimate app request.
