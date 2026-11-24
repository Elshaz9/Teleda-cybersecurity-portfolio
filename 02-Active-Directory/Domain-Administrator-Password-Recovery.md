
# Domain Administrator Password Recovery

## Active Directory Privileged Account Recovery

Date: 19 November 2026

Environment: VirtualBox AD homelab  
Domain: ad.teleda.online  
Domain Controller: Server1  
Account: Administrator  
Target Workstation: Teleda (Windows 11 Pro)

## 1. Objective

I carried out this exercise to recover administrative access to my Windows 11 domain-joined workstation (TELEDA) after I forgot my existing Domain Administrator password.

I performed this recovery from an authenticated administrative session on my domain controller, Server1.

## 2. Initial Findings

First, I confirmed that I was logged in as the administrator on Server1 using the command:

    whoami

I then inspected the password properties of the domain Administrator account on Server1.

**Recorded findings:**

- Account: Administrator
- Enabled: True
- Previous PasswordLastSet: 29 October 2026
- PasswordNeverExpires: True


## 3. Administrative Action

I reset the domain Administrator account password using the Active Directory Users and Computers GUI on Server1.

I located the Administrator account, initiated the password reset, and completed the action through the GUI.

The password reset operation was reported as successful.

I restarted my Teleda (Windows 11) and change password at logon.

## 4. Windows Security Events

During this exercise, the following Windows Security Event IDs were logged:

- **4724:** An attempt was made to reset an account's password.
- **4723:** An attempt was made to change an account's password.
- **4738:** A user account was changed.

### Investigation Findings

I correlated Events 4724 and 4738 using their matching details:

- Subject account: AD\Administrator
- Target account: AD\Administrator
- Logon ID: 0xD1E31
- Timestamp: 19 November 2026 at 11:02:53 AM

I also identified Event 4723 as a separate activity involving my Teledalase account from the previous day.

## 5. Investigation Approach

I used Windows Security Event Viewer to investigate the account activity.

I relied solely on Windows Security event logs to review and correlate the events.

## 6. Conclusion

This exercise gave me practical experience investigating privileged account recovery in an Active Directory environment.

I verified the Administrator account properties, performed a password reset from the domain controller, and examined the resulting Windows Security events.

By correlating Events 4724 and 4738 using the subject account, target account, Logon ID, and timestamp, I gained a better understanding of how Windows records administrative password-reset activity.

Status: Completed.

## 7. Skills Practised

- Active Directory administration
- Domain Administrator account management
- PowerShell
- Windows Security Event Viewer
- Event ID investigation
- Security event correlation
- Privileged account monitoring
