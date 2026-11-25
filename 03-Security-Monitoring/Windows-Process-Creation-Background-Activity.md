# Windows Process Creation Auditing – Background Activity Monitoring with Wazuh

## 1. Introduction

In this exercise, I investigated Windows Process Creation auditing in my TELEDA Active Directory lab. My objective was to understand how Windows records newly created processes, how to identify the account responsible for creating a process, and how Wazuh collects and displays these events.

I examined both interactive user activity and normal Windows background activity. This helped me understand that not every process creation event represents suspicious behaviour.

## 2. Lab Environment

| Component | Details |
|---|---|
| Active Directory Domain | ad.teleda.online |
| Domain Controller | Server1 – 192.168.10.10 |
| Windows Workstation | TELEDA – 192.168.10.20 |
| Wazuh Manager | UBUNTU-SRV – 192.168.10.30 |
| Wazuh Agent ID | 002 |
| Windows Event ID | 4688 |
| Monitoring Platform | Wazuh 4.14.10 |

## 3. Audit Policy Configuration

I configured Windows Process Creation auditing through Group Policy Management.

The following Group Policy Objects were linked to the SOC-Workstations OU:

- SOC-Workstations-Security-Baseline
- Soc-Workstations-Local-Admin

I verified the Process Creation audit policy on the TELEDA workstation using the following command:

```powershell
auditpol /get /subcategory:"Process Creation"
```

The output confirmed that both Success and Failure auditing were enabled.

This configuration allows Windows to generate security events when processes are created.

## 4. Windows Event Investigation

I examined Event ID 4688 in Windows Event Viewer. This event records the creation of a new process.

The investigation covered three examples: one interactive user process and two background processes running under the Windows SYSTEM security context.

### 4.1 Interactive User Process – Taskmgr.exe

On November 21, 2026, I observed a process creation event involving the Task Manager application.

| Field | Observed Value |
|---|---|
| Event ID | 4688 |
| User Account | AD\Teledalase |
| New Process | Taskmgr.exe |
| Parent Process | LaunchTM.exe |
| Command Line | "C:\WINDOWS\System32\Taskmgr.exe" /2 |
| Integrity Level | Medium |
| Integrity SID | S-1-16-8192 |
| Token Elevation Type | Type 1 – Default |
| Event Record ID | 35286 |
| Wazuh Agent | Teleda |
| Agent ID | 002 |
| Wazuh Rule ID | 67027 |

This event showed that the Teledalase account started Task Manager. The process ran with Medium integrity and a default token elevation type.

### 4.2 Background Process – CompatTelRunner.exe

On November 23, 2026, I observed another process creation event involving CompatTelRunner.exe.

| Field | Observed Value |
|---|---|
| Event ID | 4688 |
| User Account | TELEDA$ |
| User SID | S-1-5-18 |
| New Process | CompatTelRunner.exe |
| Parent Process | svchost.exe |
| Command Line | CompatTelRunner.exe |
| Integrity Level | System |
| Integrity SID | S-1-16-16384 |
| Event Record ID | 36793 |
| Wazuh Agent | Teleda |
| Agent ID | 002 |
| Wazuh Rule ID | 67027 |
| Event Time | November 23, 2026, 02:47:48 |

The event showed that CompatTelRunner.exe was created under the Windows SYSTEM security context.

The account name TELEDA$ appeared in the event, while the subject SID S-1-5-18 identified the Local SYSTEM account.

### 4.3 Background Process – taskhostw.exe

I also observed a second background process creation event on November 23, 2026.

| Field | Observed Value |
|---|---|
| Event ID | 4688 |
| User Account | TELEDA$ |
| User SID | S-1-5-18 |
| New Process | taskhostw.exe |
| Parent Process | svchost.exe |
| Command Line | taskhostw.exe |
| Integrity Level | System |
| Integrity SID | S-1-16-16384 |
| Event Record ID | 36869 |
| Wazuh Agent | Teleda |
| Agent ID | 002 |
| Wazuh Rule ID | 67027 |
| Event Time | November 23, 2026, 02:51:04 |

This event also showed a process running under the Windows SYSTEM security context.

The parent process was svchost.exe, and the newly created process was taskhostw.exe.

## 5. Wazuh Monitoring and Verification

After verifying the Windows events, I checked Wazuh to confirm that the events were being collected from the TELEDA workstation.

The events appeared in Wazuh with the following details:

| Field | Observed Value |
|---|---|
| Agent Name | Teleda |
| Agent ID | 002 |
| Agent IP | 192.168.10.20 |
| Decoder | windows_eventchannel |
| Windows Event ID | 4688 |
| Rule ID | 67027 |
| Rule Description | A process was created |
| Rule Level | 3 |
| Manager | UBUNTU-SRV |
| Event Location | EventChannel |

Wazuh successfully received and decoded the process creation events from the Windows workstation.

I also verified the background processes in Wazuh, including CompatTelRunner.exe and taskhostw.exe.

## 6. Investigation Observations

During this exercise, I learned that Event ID 4688 provides useful information for process investigation.

The event can help an analyst identify:

- The account associated with process creation.
- The executable that was started.
- The parent process.
- The command line used to launch the process.
- The process integrity level.
- The token elevation type.

I also observed that not every process creation event should be treated as suspicious.

The Taskmgr.exe event represented interactive user activity, while CompatTelRunner.exe and taskhostw.exe were observed running under the Windows SYSTEM security context.

Understanding the process, parent process, account and execution context is important before deciding whether an event requires further investigation.

## 7. Conclusion

This exercise helped me understand how Windows Process Creation auditing works and how Wazuh monitors Event ID 4688.

I successfully verified that the TELEDA workstation generated process creation events and that Wazuh collected and displayed them.

By examining both interactive and background processes, I gained practical experience identifying process activity, understanding security context and distinguishing normal Windows activity from events that may require further investigation.

## 8. Evidence

The following screenshots were collected during this exercise and uploaded to the 06-Evidence folder:

1. [Windows-Process-Creation-01-Audit-Policy.png](../06-Evidence/Windows-Process-Creation-01-Audit-Policy.png) – Process Creation audit policy showing Success and Failure enabled.
2. [Windows-Process-Creation-02-Taskmgr.png](../06-Evidence/Windows-Process-Creation-02-Taskmgr.png) – Windows Event Viewer showing the Taskmgr.exe process creation event.
3. [Windows-Process-Creation-03-CompatTelRunner.png](../06-Evidence/Windows-Process-Creation-03-CompatTelRunner.png) – Windows Event Viewer showing the CompatTelRunner.exe process creation event.
4. [Windows-Process-Creation-04-taskhostw.png](../06-Evidence/Windows-Process-Creation-04-taskhostw.png) – Windows Event Viewer showing the taskhostw.exe process creation event.
5. [Windows-Process-Creation-05-Wazuh-CompatTelRunner.png](../06-Evidence/Windows-Process-Creation-05-Wazuh-CompatTelRunner.png) – Wazuh showing the CompatTelRunner.exe process creation event.
6. [Windows-Process-Creation-06-Wazuh-taskhostw.png](../06-Evidence/Windows-Process-Creation-06-Wazuh-taskhostw.png) – Wazuh showing the taskhostw.exe process creation event.
