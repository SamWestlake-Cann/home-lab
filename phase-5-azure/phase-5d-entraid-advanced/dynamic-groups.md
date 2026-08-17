# Dynamic Groups — Westlake IT Solutions

Three dynamic security groups were created in the Westlake IT Solutions
tenant, each driven by user attributes set at account creation
(Department, Job Title). All three were verified to auto-populate
correctly within minutes of creation — no manual membership assignment.

## SOC-ALL
- **Rule:** `(user.department -eq "Cybersecurity")`
- **Purpose:** Captures all Cybersecurity/SOC department staff, to be used
  as the target group for stricter Conditional Access policies (compliant
  device requirement) in the next stage.
- **Verified member:** Amy Churm (Security Analyst)
- Screenshots: 42, 43

## IT-Admins-Eligible
- **Rule:** `(user.jobTitle -contains "Administrator")`
- **Purpose:** Captures any user with "Administrator" in their job title,
  intended as the target group for PIM eligible role assignment (User
  Administrator) and device-compliance CA policy.
- **Verified member:** Sam Westlake-Cann (Systems Administrator)
- Screenshots: 44, 45

## Finance-Sensitive
- **Rule:** `(user.department -eq "Finance")`
- **Purpose:** Captures Finance department staff — a high-value target
  group for stricter MFA/location-based Conditional Access controls.
- **Verified member:** Neil Cutler (Finance Manager)
- Screenshots: 46, 47

## Notes
- All groups use **Dynamic User** membership type, Security group type.
- Membership evaluated automatically by Entra ID within minutes of group
  creation — no manual "Add members" step was needed.
- These groups are designed to be reused as CA policy targets in the next
  stage (see conditional-access.md).

## Rule refinement: IT-Admins-Eligible

The original rule `(user.jobTitle -contains "Administrator")` was
identified as too broad — it incorrectly captured Rachel Cutler
(Payroll Administrator, Finance) alongside Sam Westlake-Cann (Systems
Administrator, IT Operations), since it matched on job title text
alone with no department constraint.

**Updated rule:**
This correctly scopes the group to IT Operations administrators only —
the intended target population for CA002 (compliant device
requirement) and the PIM eligible User Administrator role assignment.

**Result verified:** Rachel Cutler removed from the group; Sam
Westlake-Cann remains. Confirmed via the Members tab after the rule
update.

This is a real-world example of why dynamic group rules should be
reviewed against actual membership outcomes, not just assumed correct
once written — literal attribute matching can produce unintended
scope creep as an organisation's headcount grows.
