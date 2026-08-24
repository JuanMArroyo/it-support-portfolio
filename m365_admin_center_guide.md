# Microsoft 365 Admin Center — User & Group Provisioning Guide

A hands-on walkthrough of core Microsoft 365 administration tasks: user account creation, department group setup with shared mailboxes, group membership management, and password resets — the workflows most directly relevant to a Tier 1 IT support / help desk role.

## Overview

This guide documents a complete, realistic provisioning scenario built in a Microsoft 365 Business Basic trial tenant: creating a department group with the correct security posture, provisioning a new employee account, assigning them to the right group, and resolving the single highest-volume support ticket type — a password reset.

## Core Concept: Account Provisioning

**Provisioning** is the process of setting up a new user's access to company systems: creating their login, assigning licenses, and granting them membership in the groups relevant to their role. The reverse process — removing all access when someone leaves — is **deprovisioning**. Prompt deprovisioning matters for security; an inactive former employee's account left active is a real, common risk.

## Creating a Department Group (Microsoft 365 Group)

M365 offers several group types, each suited to a different purpose:

| Group Type | Purpose |
|---|---|
| Security group | Permissions only — controls access to a resource, no email or collaboration features |
| Microsoft 365 group | Includes a shared mailbox, shared calendar, and optional Teams/SharePoint integration |
| Distribution group | Email list only — sends one message to every member, no shared resources |
| Mail-enabled security group | Hybrid — permissions-based, but with an email address |

A **Microsoft 365 group** was used here, since it's the type that creates an actual functioning shared mailbox as part of setup.

### Setup Walkthrough

1. **Basics** — Name and description (e.g. "Sales Team" / "Group for members of the Sales Team/Department"). Clear, department-based naming matters in practice — vague names cause confusion in address books and permission audits later.

2. **Owners vs. Members** — an important distinction:
   - **Owner** — can manage the group itself: add/remove members, change settings, rename the group. Functionally similar to a department manager controlling who has access.
   - **Member** — has access to the group's shared resources (mailbox, calendar, files) but cannot manage the group's configuration.

   Microsoft requires at least one owner, and recommends two for redundancy — if one owner is unavailable, the group still has active management.

3. **Members** — anyone added here immediately gains access to everything in the group: the shared mailbox, shared calendar, and files. This is the actual mechanism behind a common real-world ticket: *"I can't access the shared Sales mailbox"* is, functionally, a group membership issue.

4. **Settings — Group email address and Privacy:**
   - The group email address (e.g. `SalesTeam@company.onmicrosoft.com`) is the real, functioning shared mailbox.
   - **Privacy** was set to **Private** rather than the default "Public." Reasoning: a Sales department's shared mailbox likely contains client and deal information that shouldn't be browsable by the entire company by default. This applies the **principle of least privilege** — access should be limited to only what's needed, not left open "just in case."
   - **Role assignment** ("allow admin roles to be assigned to this group") was left disabled — a group should never itself be capable of granting administrative privileges to its members. This is another application of least privilege: keeping a routine department group from becoming an unintended path to elevated access.

5. **Add Microsoft Teams to your group** — left enabled, which automatically creates a matching Teams channel alongside the shared mailbox, mirroring how many real companies structure department communication (shared email + shared chat/collaboration space).

## Creating a User Account

1. **Basics** — first/last name, display name, and username (which becomes the login and email prefix, e.g. `johndoe@company.onmicrosoft.com`).

2. **Password settings:**
   - **"Automatically create a password"** — left checked. The system generates a secure, complex password rather than the admin choosing one manually.
   - **"Require this user to change their password when they first sign in"** — left checked. This ensures the admin never actually knows the user's real, ongoing password — the temporary generated password is used exactly once, then replaced by something only the user knows.

3. **License assignment** — required to actually activate the account's access to Exchange email, Teams, SharePoint, etc. Licenses have a real per-user cost in a live company, so assignment (and prompt removal when someone leaves) is a task IT typically manages carefully rather than automatically.

4. **Optional settings — Roles and Groups:**
   - **Role: "User (no administrator access)"** — the correct default for a standard employee. Granting admin access to a regular account with no business justification is a real, unnecessary security risk; if that account were ever compromised, the blast radius would be the entire tenant instead of just that one user's own resources.
   - **Groups** — the new user was assigned to the "Sales Team" group created above. This is the concrete link between account creation and the department group's shared resources: the moment this assignment happens, the user gains access to the shared mailbox, calendar, and Teams channel.

5. **Profile info** (job title, department, office location) — optional, doesn't affect login or access, but populates the organization's internal directory/address book. Filled in for realism (Job title, Department) since it reflects how a real account is tied to an actual role, not just a bare login.

### Verifying the Group Assignment

After creation, the new user's membership was confirmed two ways: from the group's own **Members** list, and from the user's individual profile page. Both paths lead to the same result — useful to know, since a real ticket might have you approaching group membership from either direction depending on what's being asked.

## Password Reset

The most common Tier 1 support ticket across virtually every organization.

**Process:**
1. Locate the user in the Users list.
2. Click the "Reset password" (key icon) action.
3. Same two settings as account creation: auto-generate the password, require change on next sign-in.
4. The system displays a newly generated temporary password on screen for the admin to securely relay to the user.

**Why this pattern matters:** the admin never manually invents or retains knowledge of the user's real password at any point — before or after a reset. This is a deliberate security practice, not an incidental default.

**A related, current industry concept: Self-Service Password Reset (SSPR).** Microsoft actively surfaces this as a recommended action after a manual reset — letting users reset their own forgotten passwords via a registered phone/email/security method, without contacting IT at all. Since password resets are the highest-volume ticket type almost everywhere, SSPR is a genuine, practical way to reduce overall ticket load — worth knowing not just as a manual task, but as a piece of infrastructure that reduces the need for the manual task in the first place.

## Summary

This exercise walked through a complete, realistic provisioning lifecycle:

1. Created a department group with an appropriate security posture (Private, no unnecessary admin-role capability)
2. Provisioned a new employee account with least-privilege defaults (standard user role, forced password change)
3. Assigned the new employee to the correct group, verified via two different paths in the admin center
4. Performed a password reset following the same secure, admin-never-knows-the-password pattern
5. Identified a relevant infrastructure improvement (SSPR) that a security-minded technician would recognize as valuable beyond just performing the manual task

Every decision in this workflow — group privacy, role assignment, password handling — was made by deliberately applying the **principle of least privilege**, not just following default settings.
