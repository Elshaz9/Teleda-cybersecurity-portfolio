# Active Directory Employee Password Reset Monitoring

## 1. Overview

On 19 November 2026, I investigated an administrative password-reset activity in my TELEDA Active Directory lab.

The investigation focused on identifying who initiated the password-reset action and which employee account was targeted.

I used Wazuh to review Windows Security events collected from my domain controller, Server1.

## 2. Lab Environment

- **Environment:** VirtualBox Active Directory homelab
- **Domain:** ad.teleda.online
- **Domain Controller:** Server1
- **Domain Controller IP:** 192.168.10.10
- **Target Account:** mibrahim
- **Initiating Account:** Administrator
- **Monitoring Platform:** Wazuh

## 3. Investigation Findings

I located Windows Security Event ID 4724 in Wazuh.

The event showed that the Administrator account initiated a password-reset action targeting the mibrahim account.

**Recorded details:**

- **Event ID:** 4724
- **Subject Account:** AD\Administrator
- **Target Account:** AD\mibrahim
- **Date:** 19 November 2026
- **Time:** 14:02:44
- **Event Record ID:** 123202
- **Agent:** Server1

Event ID 4724 records an attempt to reset an account's password. The event identifies the initiating account and the account targeted by the action.

## 4. Supporting Security Event

I also identified Event ID 4738 involving the same accounts.

**Recorded details:**

- **Event ID:** 4738
- **Subject Account:** AD\Administrator
- **Target Account:** AD\mibrahim
- **Date:** 19 November 2026
- **Time:** 14:02:44
- **Event Record ID:** 123206

Event ID 4738 records that a user account was changed. I reviewed it as supporting context for the administrative activity.

This event does not independently confirm a password reset. Event ID 4724 is the primary evidence for the password-reset action.

## 5. Investigation Approach

I used Wazuh Discover to search Windows Security events collected from Server1.

I reviewed the event IDs, subject account, target account, timestamps, and event record IDs to understand the administrative activity.

I correlated the Event ID 4724 password-reset record with the related Event ID 4738 account-change record.

## 6. Conclusion

This investigation gave me practical experience monitoring administrative password-reset activity in Active Directory.

I identified the Administrator account as the account that initiated the password-reset action and mibrahim as the target account.

I also reviewed the related account-change event and learned the difference between Event ID 4724 and Event ID 4738.

The investigation demonstrated how Wazuh can help a SOC analyst monitor privileged account activity and investigate changes involving domain user accounts.

**Status:** Completed.

## 7. Skills Practised

- Active Directory account monitoring
- Windows Security Event investigation
- Wazuh Discover and event filtering
- Event ID 4724 analysis
- Event ID 4738 analysis
- Security event correlation
- Privileged account activity monitoring
