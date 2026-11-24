
# SOC-002: Failed SSH Login Investigation

## 1. Incident Overview

I investigated a failed SSH login attempt from my Kali Linux machine with the IP (192.168.10.40) to my UbuntuSuricata machine with the IP (192.168.10.50).

The login attempt used the username `vboxuser` and targeted my SSH port 22.

Wazuh detected the failed authentication activity through the authentication logs.


## 2 Investigation Objective

The objective of this investigation was to:

- Identify the source of the SSH login attempt
- Determine the targeted machine, IP and username
- Review the authentication logs on Wazuh
- Understand why the login failed
- Check if there was eventually successful login
- Document my findings

## 3 Investigation Environment

Components      |   Details

SIEM Monitoring tool |  Wazuh 4.14.10

Network username     |  UbuntuSuricata

Attacking system     |  Kali linux

Target system        |  UbuntuSuricata

Source IP            |  192.168.10.40

Destination IP       |  192.168.10.50

Detection source     |	journald / sshd



## 4 Detection Analysis

Wazuh detected suspicious ssh login attempt from kali 192.168.10.40 targeting UbuntuSuricata 192.168.10.50

The detection was triggered by the main rule 5760;

sshd: authentication failed

Severity: 5

Protocol: SSH

Source port: 43868

Destination port: 22


## 5 Investigation Findings 

Kali (192.168.10.40) attempted SSH authentication to UbuntuSuricata (192.168.10.50) using the vboxuser account. 

Authentication failed, there was no successful login or compromise.


## 6 MITRE ATT&CK Mapping

T1021.004 – SSH


## 7 Conclusions

One failed SSH login attempt was performed by Kali. Wazuh successfully detected and classified the alert.

There was no successful SSH login by the attacker (KALI).


## 8 Remediation and Recommendation

Monitor repeated SSH failures.
Use strong passwords or SSH keys.
Restrict SSH access to trusted hosts.
Investigate repeated authentication failures.






