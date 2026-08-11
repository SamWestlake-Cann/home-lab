# Phase 5d: Entra ID Advanced — Identity Governance

Advanced Microsoft Entra ID identity governance built and tested in a
live tenant (`WestlakeCann.onmicrosoft.com`), simulating identity
administration for a fictional 40-person MSP, **Westlake IT Solutions**.

This phase covers three core areas of real-world identity governance:
Dynamic Groups, Conditional Access, and Privileged Identity Management
(PIM) — the same controls used by MSPs and security-conscious
organisations to manage access at scale.

## Contents

- [`01-scenario-design.md`](01-scenario-design.md) — company scenario,
  department structure, full user roster (including the growth round),
  and dynamic group rule verification notes
- [`dynamic-groups.md`](dynamic-groups.md) — three dynamic security
  groups, their rules, and verified auto-population behaviour
- [`conditional-access.md`](conditional-access.md) — four Conditional
  Access policies (MFA baseline, device compliance, legacy auth block,
  guest MFA), all built and tested in Report-only mode
- [`pim-configuration.md`](pim-configuration.md) — PIM eligible role
  assignment and a full eligible-to-active activation test, performed
  as the actual end user rather than just configured from the admin
  side

## What was built

**Company scenario:** Westlake IT Solutions — 14 fictional staff across
6 departments (IT Operations, Service Desk, Cybersecurity, Finance,
Sales & Client Services, and a planned Contractors/External guest
tier), each with realistic job titles and attributes driving
downstream group and policy logic.

**Dynamic Groups (3):**
- `SOC-ALL` — auto-populates Cybersecurity department staff
- `IT-Admins-Eligible` — auto-populates any user with "Administrator"
  in their job title (including an instructive edge case — see
  scenario design notes)
- `Finance-Sensitive` — auto-populates Finance department staff

**Conditional Access policies (4, Report-only):**
- CA001 — Require MFA for all users
- CA002 — Require compliant device for IT-Admins-Eligible
- CA003 — Block legacy authentication
- CA004 — Require MFA for guests and external users

**Privileged Identity Management:**
- User Administrator role configured as Eligible (not standing) for
  the IT Operations user
- Full activation cycle tested by signing in as that user directly:
  eligible → activation request with justification → active with
  countdown expiry → deactivate option

## Key practices demonstrated

- Real tenant admin account excluded from every Conditional Access
  policy as a break-glass safeguard against lockout
- Security Defaults disabled deliberately to enable Conditional Access,
  understanding the trade-off and sequencing required
- All CA policies built and verified in Report-only mode before any
  future move to enforced (On) state
- Dynamic group rules verified against real attribute-driven
  evaluation, not just configured and assumed correct
- PIM tested end-to-end as a genuine standard user, not just
  configured from the administrator's perspective

## Screenshots

Numbered sequentially from 39 (continuing the home lab's overall
numbering convention). See individual markdown files for the specific
screenshot references tied to each section.

## Next steps

- Add a real Guest/External contractor account to properly test CA004
- Configure PIM approval workflows (requiring a second admin to
  approve activation requests)
- Review PIM audit history and Conditional Access sign-in logs once
  policies have accumulated real activity
- Consider tightening the `IT-Admins-Eligible` dynamic group rule to
  combine job title AND department, addressing the Rachel Cutler
  edge case documented in `01-scenario-design.md`
