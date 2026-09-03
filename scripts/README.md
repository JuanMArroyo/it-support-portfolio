🐍 Python Automation Scripts for IT Help Desk
A collection of practical Python + PowerShell automation tools designed for real-world IT Help Desk and Tier 1/Tier 2 workflows. These scripts demonstrate Active Directory automation, bulk operations, and common troubleshooting tasks used in enterprise environments.

📌 1. Active Directory User Provisioning Script
What it does:  
Automates the creation of new user accounts in Active Directory by collecting user details in Python and generating the appropriate PowerShell commands. This script simulates real onboarding workflows by creating accounts with names, usernames, passwords, and initial attributes.

Use case:  
IT onboarding, new hire provisioning, lab environments, and identity management automation.

![]

📌 2. Mass Password Reset Script
What it does:  
Reads a CSV file containing usernames and automatically resets passwords for all listed accounts. This script is ideal for bulk operations such as company-wide password rotations, compromised credential events, or lab resets.

Use case:  
Security events, password policy enforcement, bulk account maintenance.

![]

📌 3. Account Unlock + Status Checker
What it does:  
Checks whether a user account is locked and unlocks it automatically. This script mimics one of the most common Tier 1 help desk tasks and provides quick resolution for login issues caused by lockouts.

Use case:  
Daily help desk tickets, login troubleshooting, account lockout resolution.

![]

📌 4. Shared Drive Access Script (Group Membership Automation)
What it does:  
Adds users to the correct security groups based on department or access requirements. This script automates shared drive permissions, application access, and role-based access control by modifying AD group memberships.

![]

Use case:
