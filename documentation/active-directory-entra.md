# Active Directory vs. Entra ID

## What is Active Directory (AD)?

AD is a centralized database for storing credentials — users, devices, and the permissions attached to them. It's mainly used for diagnosing and fixing on-prem related issues, like a user saying "I can't log into this computer at all" or "I can't access this file."

## What is Entra ID?

Entra ID takes AD's concept and moves it into the cloud, so it can authenticate access to cloud services like Teams, SharePoint, and Outlook — not just local network resources. It also has cloud-native security features AD doesn't have on its own, like Conditional Access and MFA policies. The difference is it can't push local machine settings the way on-prem Group Policy can (like applying settings to specific devices offline).

## How They Work Together

In most real companies, AD and Entra ID aren't an either/or — they're run together as a hybrid setup. A tool called Entra ID Connect syncs AD-managed credentials to the cloud, so it's the same account working in both places instead of two separate logins. That's what gives IT more control while letting employees access everything — on-prem and cloud — with one set of credentials.

## Matching the Symptom to the Right Tool

This is the part that actually matters on a ticket. Based on what the user reports:

- **"I can't log in at all"** → AD issue — check the on-prem account/credentials
- **"Teams won't open"** → M365 Admin Center issue — check licensing/app access
- **"I'm getting an API auth failure"** → Entra ID issue — check app registrations, client secrets, sign-in logs

Knowing which tool owns which layer means less time guessing and less time escalating something that could've been checked in two minutes.
