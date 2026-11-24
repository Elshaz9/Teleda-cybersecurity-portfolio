# SOC Investigations

This section contains structured case studies from controlled security monitoring exercises.

## Investigation register

| ID | Case | Primary evidence | Status |
|---|---|---|---|
| SOC-001 | Possible TCP SYN port scan | Suricata alert; custom signature SID 1000001 | Completed |
| SOC-002 | Failed SSH login activity | Linux SSH authentication events; Wazuh | Completed |
| SOC-003 | Windows file access auditing | Windows Security Event ID 4663; Wazuh archives | Write-up pending |

## Case study standard

Each report should explain the alert context, scope, evidence sources, investigation steps, findings, disposition, and lessons learned. Findings must distinguish observed facts from analyst interpretation.

Public reports must use sanitized screenshots and avoid publishing credentials or unnecessary internal network details.
