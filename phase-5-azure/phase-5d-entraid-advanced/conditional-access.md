# Conditional Access — Westlake IT Solutions

Four Conditional Access policies were built in the Westlake IT Solutions
tenant, all in **Report-only mode** (not enforced) so behaviour could be
verified before any policy is switched to On. Every policy excludes the
real tenant admin account (Sam@WestlakeCann.onmicrosoft.com) as a
break-glass safeguard against accidental lockout — standard practice
before enabling CA in any live tenant.

Security Defaults was disabled tenant-wide to allow Conditional Access
policies to be created (the two features cannot run simultaneously).

## CA001 - Require MFA for all users
- **Users:** All users (admin account excluded)
- **Target resources:** All resources
- **Grant:** Require multifactor authentication
- **Purpose:** Baseline MFA enforcement across the organisation.

## CA002 - Require compliant device for IT Admins
- **Users:** IT-Admins-Eligible (dynamic group)
- **Target resources:** All resources
- **Grant:** Require device to be marked as compliant
- **Purpose:** Restrict administrative access to managed/compliant
  devices only. Non-Windows platforms (macOS, iOS, Android, Linux)
  excluded from the report-only compliance check to avoid unnecessary
  certificate prompts in this lab, since no Intune-enrolled devices
  exist in this tenant.
- **Note:** Real-world enforcement of this policy requires Intune device
  compliance policies, which are outside the scope of this lab tenant.

## CA003 - Block legacy authentication
- **Users:** All users (admin account excluded)
- **Target resources:** All resources
- **Conditions - Client apps:** Exchange ActiveSync clients, Other
  clients (legacy/basic auth protocols). Browser and modern
  mobile/desktop clients excluded.
- **Grant:** Block access
- **Purpose:** Legacy authentication protocols cannot support MFA,
  making them a common target for password-spray attacks. Blocking
  them is a standard, high-value hardening step in real MSP
  environments.

## CA004 - Require MFA for guests and external users
- **Users:** Guest or external users (all external user types)
- **Target resources:** All resources
- **Grant:** Require multifactor authentication
- **Purpose:** Enforce stricter authentication for any external/guest
  account, since contractors and third parties represent higher risk
  than internal staff.

## Notes
- All 4 policies remain in Report-only mode. Before switching any policy
  to On, sign-in logs / Insights and reporting should be reviewed to
  confirm the policy behaves as expected and does not unintentionally
  block legitimate access.
- Screenshots: 48 (CA001), 49 (CA002), 50 (CA003), 51 (all four policies)

## Enforcement rollout

All 4 Conditional Access policies were moved from Report-only to
**enforced (On)**, following a real review-then-enforce process:

1. Generated real sign-in activity from multiple users
2. Reviewed each sign-in's **Report only** tab under Sign-in logs to
   confirm each policy correctly scoped to its intended users, with
   no unintended blocking
3. Confirmed the admin exclusion (both the primary Global Admin and
   the Emergency Access break-glass account) correctly resulted in
   "Not applicable" across all 4 policies for those accounts
4. Switched CA003 (lowest risk — legacy auth block) to On first
5. Verified live enforcement by signing in as Stephanie Westlake
   (Service Desk) and confirming MFA was genuinely prompted and
   required — not just simulated in Report-only
6. Switched the remaining policies (CA001, CA002, CA004) to On

**Note on CA002 in practice:** since this lab tenant has no
Intune-enrolled/compliant devices, IT-Admins-Eligible members (Sam
Westlake-Cann, Rachel Cutler) cannot currently satisfy the compliant
device requirement in real sign-in. This is expected — CA002 remains
primarily an architectural demonstration of role-scoped device
compliance policy, rather than something enforced against real
devices in this lab.

All 4 policies: **State = On** as of 17/08/2026.
