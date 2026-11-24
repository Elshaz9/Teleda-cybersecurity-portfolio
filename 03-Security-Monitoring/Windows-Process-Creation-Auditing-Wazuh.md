
# Windows Process Creation Auditing and Wazuh Monitoring

**SOC Portfolio Report**

**Environment:** TELEDA Active Directory Lab  
**Workstation:** TELEDA (Windows 11 Pro)  
**Domain:** ad.teleda.online  
**Wazuh Server:** UBUNTU-SRV (192.168.10.30)  
**Wazuh Agent:** Teleda (Agent ID 002)  
**Date:** 21 November 2026

## 1. Introduction

In this exercise, I configured Windows process creation auditing on my TELEDA workstation and connected the monitoring to Wazuh.

The main purpose was to learn how Windows records programs and commands executed by users and how a SOC analyst can use Wazuh to monitor this activity.

I also wanted to understand the difference between activities performed by a normal domain user and an administrator.

## 2. Configuring the Group Policy

I used Group Policy Management on my Windows Server to configure the workstation security settings.

I worked with two Group Policy Objects (GPOs):

- SOC-Workstations-Security-Baseline
- Soc-Workstations-Local-Admin

Both policies were linked to the SOC-Workstations OU, where the TELEDA computer account is located.

Inside the security baseline GPO, I enabled process creation auditing under:

Computer Configuration → Policies → Windows Settings → Security Settings → Advanced Audit Policy Configuration → System Audit Policies → Detailed Tracking

I configured Process Creation to audit both Success and Failure.

I also enabled command line recording under:

Computer Configuration → Policies → Administrative Templates → System → Audit Process Creation

The setting was **Include command line in process creation events**.

This allows Windows to record the command used to launch a program, rather than only recording the program name.

I also enabled the policy that forces advanced audit policy settings to override older audit policy settings.

Most of this configuration was completed using the graphical interface.

## 3. Checking That the Policies Were Applied

After configuring the GPOs, I updated the workstation policies with the command #gpupdate /force 
And checked that they were working. 

I used Group Policy Results and Command Prompt to verify the configuration.

The checks confirmed that:

- Both workstation GPOs were applied to TELEDA.
- Process Creation auditing was enabled for Success and Failure.
- Command line recording was enabled.

I also checked the Windows registry and confirmed that:

**ProcessCreationIncludeCmdLine_Enabled = 0x1**

This showed that Windows was configured to include command line information in process creation events.

## 4. Testing Windows Event ID 4688

After completing the configuration, I checked the Windows Security event log.

I used Event Viewer and Command Prompt to look for Event ID 4688, which means a new process has been created.

I found several events showing programs that had been started on the workstation.

The events included information such as:

- Name of the program.
- Command used to start the program.
- User account that started it.
- Parent process.
- User security identifier (SID).
- Logon ID.
- Token elevation type.
- Integrity level.

This confirmed that Windows was recording the process activity as expected.

## 5. Checking the Events in Wazuh

After confirming that Windows was generating Event ID 4688, I checked whether the events were reaching my Wazuh server.

I used the Ubuntu terminal to search the Wazuh archives and then opened Wazuh Discover in the dashboard.

I found Event ID 4688 events from the TELEDA workstation.

The events were decoded by Wazuh using:

- **Decoder:** windows_eventchannel
- **Rule ID:** 67027
- **Description:** A process was created.
- **Rule level:** 3

I used the following query in Wazuh Discover to find activity from the SOC administrator:

    agent.name:"Teleda" AND data.win.system.eventID:"4688" AND data.win.eventdata.subjectUserName:"Soc.Admin"

I also used this query to find activity from the normal domain user:

    agent.name:"Teleda" AND data.win.system.eventID:"4688" AND data.win.eventdata.subjectUserName:"Teledalase"

Both queries returned results.

## 6. What I Found During the Investigation


I found an event showing activity from the account AD\Soc.Admin.

**Process:** wevtutil.exe

This program was used to query the Windows Security event log.

The command was:

    wevtutil qe Security /q:"*[System[(EventID=4688)]]" /c:5 /rd:true /f:text

The parent process was cmd.exe, meaning the command was started from Command Prompt.

I also found the following information:

- **Token elevation type:** %%1937 (Type 2 — Elevated token).
- **Integrity level:** High.
- **Event ID:** 4688.
- **Event Record ID:** 34902.
- **Wazuh rule:** 67027.

This showed that Wazuh could identify the administrator account and record that the process was running with elevated privileges.

### B. Normal Domain User Activity

I also found an event from my normal domain account, AD\Teledalase.

**Process:** mmc.exe

The command showed that the user opened Windows Event Viewer.

The parent process was explorer.exe, which means the program was started through the Windows desktop.

The event contained:

- **Token elevation type:** %%1936 (Type 1 — Default token).
- **Integrity level:** Medium.
- **Event ID:** 4688.
- **Event Record ID:** 34877.
- **Wazuh rule:** 67027.

This showed that Windows and Wazuh were also recording activity performed by a normal user without administrator privileges.

## 7. Comparing Both Accounts

| Information | SOC Administrator | Normal User |
|---|---|---|
| Account | Soc.Admin | Teledalase |
| Program | wevtutil.exe | mmc.exe |
| Parent process | cmd.exe | explorer.exe |
| Token type | Elevated | Default |
| Integrity level | High | Medium |
| Event ID | 4688 | 4688 |

From the results, I could see the difference between the two accounts.

The SOC administrator was running a command with an elevated token and High Integrity, while the normal user was running Event Viewer with a default token and Medium Integrity.

This helped me understand how Windows records user activity and privilege levels.

## 8. What I Learned

During this exercise, I learned:

1. How to configure Windows audit policies using Group Policy Management.
2. How to enable process creation auditing.
3. How to enable command-line information in Event ID 4688.
4. How to use Event Viewer to check Windows Security events.
5. How Windows records the user and program responsible for starting a process.
6. How to use Wazuh Discover to search for specific Windows events.
7. How to compare standard-user activity with administrator activity.
8. Why process monitoring is important.

I also learned that a process creation event is not automatically a security threat. Every command, user account, parent process, and other available information must be examined before deciding whether the activity is suspicious or benign.

## 9. Conclusion

I successfully configured and tested Windows process creation auditing on my TELEDA workstation.

I used the graphical interface for most of the Group Policy configuration and used Command Prompt and the Ubuntu terminal to verify the settings and check the collected events.

I confirmed that Windows was generating Event ID 4688 and that Wazuh was receiving and displaying these events.

I was also able to find activity from both my normal domain account and my SOC administrator account and compare their privilege levels.

This exercise gave me practical experience with Windows auditing, Group Policy, Active Directory, Event Viewer, and Wazuh SIEM monitoring.

**Status:** Completed and verified.

## 10. Screenshot Evidence

The following screenshots support the configuration and investigation:

1. Group Policy Management showing the workstation security baseline.
2. Process Creation audit policy configured in the GPO.
3. Include command line in process creation events setting enabled.
4. Group Policy Results showing that the GPOs apply to TELEDA.
5. Command Prompt showing the effective audit policy.
6. Windows Security Event Viewer showing Event ID 4688.
7. Wazuh Discover showing the Soc.Admin process event.
8. Wazuh Discover showing the Teledalase process event.

## Skills Practised

- Active Directory
- Group Policy Management
- Windows Security Auditing
- Event Viewer
- Wazuh SIEM
- Basic SOC Investigation
