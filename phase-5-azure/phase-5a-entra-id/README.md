# Phase 5a — Microsoft Entra ID Identity Lab

## Overview
Hands-on lab applying Microsoft Learn Entra ID / Active Directory
theory in a live Azure tenant. Covers user provisioning, group-based
access, administrative units, and Conditional Access / Security Defaults.

## Environment
- Azure Free Account
- Tenant: samwestlakecann1gmail.onmicrosoft.com

## Tasks
- [x] Create test users
- [x] Create security group
- [x] Create administrative unit
- [x] Investigate Conditional Access → pivot to Security Defaults

## Log

### 5 Aug 2026 — Setup
- Created phase-5-azure folder structure
- Confirmed Azure budget alert active ($150 threshold, 50/80/100% alerts)

### 5 Aug 2026 — Test users
- Created two test users in Entra ID: Amy Churm and Elsie Westlake-Churm
- Credentials stored locally only (Mac notes app), not committed to repo
- Screenshots: 25a-entra-user-amy-properties.png, 25b-entra-user-elsie-properties.png

### 5 Aug 2026 — Security group
- Created security group "Sydney-IT-Support" with description
- Added both test users as members
- Screenshot: 26-entra-security-group-created.png

### 5 Aug 2026 — Administrative unit
- Created Administrative Unit "Sydney-IT-Support-AU"
- Scoped both test users as members
- Cloud equivalent of on-prem AD Organisational Units
- Screenshot: 27-entra-administrative-unit.png

### 5 Aug 2026 — Conditional Access → Security Defaults pivot
- Attempted to create a Conditional Access policy scoped to the
  Sydney-IT-Support group requiring MFA
- Blocked: Conditional Access requires Entra ID P1 licensing; free
  tenant defaults to Entra ID Free
- Attempted P1 trial activation via M365 Admin Center; blocked again
  as the tenant's admin identity is a personal Microsoft account
  (guest/global admin), not a native work/school account eligible
  for M365 Admin Center trials
- Confirmed Security Defaults already enabled by default on the
  tenant — Microsoft's free-tier baseline, enforcing MFA tenant-wide
  (not group-scoped, unlike Conditional Access)
- Documents understanding of Entra ID licensing tiers and the
  practical tradeoffs between Security Defaults and Conditional Access
- Screenshot: 28-entra-security-defaults-enabled.png

## Key takeaways
- Practical experience provisioning users, groups, and administrative
  units in Microsoft Entra ID, directly applying prior Microsoft Learn
  theory in a live tenant
- Encountered and worked through a real licensing constraint
  (Conditional Access requiring P1) rather than a scripted lab —
  reflects the kind of troubleshooting expected in an entry-level
  sysadmin/support role
- Understands the distinction between Security Defaults (free,
  tenant-wide) and Conditional Access (licensed, granular/scoped)
