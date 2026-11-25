
# SOC-001: Suricata TCP SYN Port Scan Investigation

## 1. Incident Overview

I investigated a suspected TCP SYN port scan originating from my Kali Linux machine with the IP (192.168.10.40) and targeting Server1 with IP (192.168.10.10).
Suricata detected the activity using my local detection rule, SID 1000001:
`LOCAL Possible TCP SYN Port Scan from Kali to Server1`
The alert was classified as Attempted Information Leak with severity (Medium).


## 2. Investigation Objective

The objective of this investigation was to:

- Investigate suspicious TCP SYN scanning activity.
- Determine the source and destination systems.
- Examine Suricata alerts and network event data.
- Identify the targeted TCP ports.
- Assess the security significance of the activity.
- Document findings and recommend appropriate defensive actions.

- ## Investigation Environment

- |Components | Details|

- Network Monitoring tool   | Suricata 8.0.8
- Network Username          | UbuntuSuricata
- Attacking System          | Kali Linux
- Target System             | Server1
- Source IP                 | 192.168.10.40
- Destination Ip            | 192.168.10.10
- Detection Rule SID        | 1000001 |
- Evidence Source           | eve.json |

- ## Detection and Analysis

- Suricata detected suspicious TCP SYN ports scanning activity from Kali Linux targeting server1.

- The detection was triggered by the custom local rule:

- Rule SID: 1000001
- Rule Name: LOCAL Possible TCP SYN Port Scan from Kali to Server1
- Severity: 2 (Medium)
- Protocol: TCP
- Source Port: 45533
- Destination Port: 440

The alert was recorded in Suricata's EVE JSON log (`eve.json`).


## 5. Investigation Findings

-After reviewing and analysing the Windows Event Logs and Suricata logs, I identified TCP SYN port scanning activity originating from Kali Linux and targeting Server1.

-The scan successfully performed port discovery. However, I found no evidence indicating successful exploitation of any ports or services.

-I also correlated timestamps from the Windows Event Logs and Suricata EVE JSON logs. This helped me establish a timeline of activity.


## 6. MITRE ATT&CK Mapping
 
- T1046

The activity maps to MITRE ATT&CK T1046 because the observed behaviour involved scanning a target system to discover network services and ports.

## 7. Conclusion

I concluded that the TCP SYN port scan from Kali Linux to Server1 was successful in performing network port discovery.
However, the investigation did not identify a successful exploitation of any port.


## 8. Remediation and Recommendations

- Conduct routine authorised port scans to identify exposed services and unnecessary open ports.
- Restrict access to services using appropriate firewall rules.
- Disable unnecessary services and close unused ports.
- Keep operating systems and network services updated with security patches.
- Enforce strong passwords and appropriate authentication controls.
- Monitor network traffic for unusual scanning activity and investigate suspicious alerts.

  ## 9. Evidence

  [Open SOC-001 original Suricata alert](https://github.com/Elshaz9/Teleda-cybersecurity-portfolio/blob/main/06-Evidence/SOC-001-Suricata-Original-Alert.png?utm_source=chatgpt.com)




