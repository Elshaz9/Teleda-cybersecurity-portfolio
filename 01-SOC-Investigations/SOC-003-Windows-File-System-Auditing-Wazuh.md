
# SOC-003: Windows File System Auditing and Wazuh Investigation

**SOC Portfolio Report**

**Exercise date:** 20 November 2026  
**Environment:** Virtual Active Directory and SOC Lab

## 1. Executive Summary

I performed a Windows file system auditing exercise to monitor access to the SOC department's shared folder. The objective was to verify that file access by an ordinary domain user could be recorded in Windows Security logs and collected by Wazuh.

I used Amina Yusuf (AD\ayusuf), a simulated Level 1 SOC Analyst, to test read-only access to a file in the SOC shared folder.

Windows successfully generated Security Event ID 4663, recording the user's access to the file. However, the event was initially missing from Wazuh because the Server1 agent configuration explicitly excluded Event ID 4663.

I investigated the issue, corrected the event collection filter, restarted the Wazuh agent, and repeated the test. The event subsequently appeared in Wazuh archives and was successfully located in Discover.

This exercise demonstrated Windows security auditing, access monitoring, SIEM troubleshooting, event forwarding, and evidence validation.

## 2. Lab Environment

| Component | Configuration |
|---|---|
| Domain | ad.teleda.online |
| Domain Controller | Server1 |
| Server operating system | Windows Server 2025 |
| Server1 IP | 192.168.10.10 |
| Windows client | TELEDA |
| Client operating system | Windows 11 Pro |
| Wazuh manager | UBUNTU-SRV |
| Wazuh version | 4.14.10 |
| Test account | AD\ayusuf |
| Shared folder | \\192.168.10.10\TELEDA-Shared\SOC |

## 3. Objective

My objectives were to:

- Enable Windows auditing for file system access.
- Configure auditing on the SOC shared folder.
- Test a real user's access to an audited file.
- Identify the resulting Windows Security event.
- Forward the event to Wazuh.
- Investigate and resolve any SIEM collection problems.
- Verify the event in Wazuh Discover.

## 4. Windows File System Auditing Configuration

On Server1, I enabled the following policy:

Local Security Policy → Security Settings → Advanced Audit Policy Configuration → System Audit Policies → Object Access → Audit File System

I enabled both:

- Audit Success
- Audit Failure

I verified the configuration using:

    auditpol /get /subcategory:"File System"

The result confirmed:

    Object Access
      File System    Success and Failure

This ensures Windows can generate audit events for successful and unsuccessful access attempts that match the configured auditing rules.

## 5. SOC Folder Auditing Configuration

I configured a System Access Control List (SACL) on:

    C:\TELEDA-Shared\SOC

The auditing principal was:

**Authenticated Users**

The audit type was Success and Failure.

The audited permissions included:

- List folder / read data
- Create files / write data
- Create folders / append data
- Delete subfolders and files
- Delete
- Change permissions

The auditing rule applied to the folder, subfolders, and files.

## 6. Practical Access Test

I used Amina Yusuf's domain account:

**AD\ayusuf**

Amina accessed the test file through the network share:

    \\192.168.10.10\TELEDA-Shared\SOC\SOC-Audit-Test.txt

Her access was read-only, as intended by the department's permissions.

She could open and read the file but could not create new files in the SOC folder.

This confirmed that the existing access restrictions remained effective during the audit exercise.

## 7. Windows Security Event Investigation

I examined the Security log on Server1 using Event Viewer:

Event Viewer → Windows Logs → Security

I located Event ID 4663.

The event recorded the following:

| Field | Recorded value |
|---|---|
| Event ID | 4663 |
| Account | ayusuf |
| Domain | AD |
| Object type | File |
| Object name | C:\TELEDA-Shared\SOC\SOC-Audit-Test.txt |
| Access | ReadData |
| Access mask | 0x1 |
| Severity | AUDIT_SUCCESS |
| Event Record ID | 128542 |

The event message stated:

An attempt was made to access an object.

The ReadData permission and access mask 0x1 indicate that the audited file-read right was used successfully.

This was the first confirmation that Windows was recording Amina's file access.

## 8. Wazuh SIEM Investigation

### 8.1 Initial Problem

Although Windows Event Viewer contained the 4663 event, I could not initially find it in Wazuh Discover.

During troubleshooting, I inspected the Server1 Wazuh agent configuration file:

    C:\Program Files (x86)\ossec-agent\ossec.conf

Within the Windows Security event collection configuration, I discovered that Event ID 4663 was explicitly excluded by the event query filter.

This explained why the event was present in Windows but absent from Wazuh.

### 8.2 Root Cause

The agent's Security event subscription contained this exclusion:

**EventID = 4663**

As a result, the Wazuh agent was filtering out the file access events before forwarding them to the manager.

### 8.3 Corrective Action

I performed the following remediation:

1. Created a backup of the existing ossec.conf configuration.
2. Opened the configuration file in Notepad.
3. Removed only the exclusion for Event ID 4663.
4. Preserved the other event exclusions.
5. Saved the configuration.
6. Restarted the Wazuh agent service using PowerShell:

       Restart-Service -Name WazuhSvc

I verified that the service returned to the Running state.

### 8.4 Verification in Wazuh Archives

After generating a fresh file access event, I checked the Wazuh manager's archive log.

This confirmed that the event had successfully reached the Wazuh manager.

### 8.5 Verification in Wazuh Discover

I selected the following data view:

    wazuh-archives-*

I then located the event in Discover and inspected its expanded fields.

The indexed event contained:

| Field | Value |
|---|---|
| agent.name | Server1 |
| agent.id | 001 |
| agent.ip | 192.168.10.10 |
| data.win.system.eventID | 4663 |
| data.win.eventdata.subjectUserName | ayusuf |
| data.win.eventdata.objectName | C:\TELEDA-Shared\SOC\SOC-Audit-Test.txt.txt |
| data.win.eventdata.accessMask | 0x1 |
| data.win.system.severityValue | AUDIT_SUCCESS |

The event was indexed in:

    wazuh-archives-4.x-2026.11.21

This was the final confirmation that the Windows event was successfully collected, forwarded, indexed, and searchable in Wazuh.

### 8.6 Event Forwarding Time

The recorded timestamps were:

| Source | Timestamp (UTC) |
|---|---|
| Windows event | 04:51:41.544 |
| Wazuh archive | 04:51:45.708 |

The timestamps show that the event appeared in the Wazuh archive approximately four seconds after the Windows event was recorded.

## 9. Skills Practised as an Analyst

This exercise helped me practise:

- Monitoring user access to sensitive departmental files.
- Investigating Windows Security Event ID 4663.
- Identifying the account responsible for an audited action.
- Validating endpoints in a SIEM.
- Troubleshooting missing security events.
- Confirming that collected events are searchable.

### Additional Technical Skills

This exercise also helped me learn:

- Configuring Windows security audit policies.
- Managing NTFS permissions and auditing rules.
- Troubleshooting Windows event collection.
- Backing up and editing agent configuration files.
- Restarting and verifying Windows services.
- Maintaining evidence of configuration changes.



### 10. Screenshot Filenames

- 01-Windows-File-System-Audit-Policy.png
- 02-SOC-Folder-SACL-Configuration.png
- 03-Amina-Yusuf-Read-Only-Access.png
- 04-Windows-Event-Viewer-4663.png
- 10-Wazuh-Archive-Event-4663-Amina-Yusuf.png

## 11. Conclusion

I successfully implemented and tested Windows file system auditing for the SOC department's shared folder.

During the investigation, I identified a Wazuh agent configuration filter that prevented Event ID 4663 from being forwarded to the SIEM. I corrected the filter, restarted the agent, generated a fresh test event, and verified that the event appeared in Wazuh archives and Discover.

This exercise gave me practical experience with Windows file auditing, Security Event ID 4663, Wazuh agent troubleshooting, event forwarding, and SIEM investigation.
