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
