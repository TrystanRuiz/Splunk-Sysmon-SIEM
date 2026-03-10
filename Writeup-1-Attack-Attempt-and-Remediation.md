# Splunk SIEM Lab #01 — Brute Force Attack Detection & Remediation

---

## Overview

This lab was conducted in a personal home lab environment running Splunk Enterprise as the SIEM. The setup consists of a Kali Linux machine acting as the attacker and a Windows host acting as the target, both on the same local network (`192.168.1.0/24`). The Windows host is configured to forward Windows Event Logs to Splunk via a Universal Forwarder, and Sysmon is installed to provide enhanced process-level and network telemetry beyond what standard Windows Event Logging captures. The goal of this lab was to simulate a brute force attack against the Windows host, detect it through Splunk, and apply remediation steps.

---

## Attack

A brute force attack was initiated from the Kali machine (`192.168.1.75`) against the Windows host, generating a high volume of failed authentication attempts in a short timeframe. As seen in the screenshot below, the attack was launched from Kali targeting the Windows host across the local network.

![Kali initiating connection attempts against the target](pictures/attackattemptkali.png)

---

## Detection

Splunk detected the activity through the Failed Logons panel on the SIEM dashboard. This panel surfaces Windows Event ID 4625, which is the Windows Security event generated every time an authentication attempt fails on a Windows system. A spike of 15 Event ID 4625 entries was observed within a short window. A single user accidentally mistyping their password would not generate 15 consecutive failed logon attempts in rapid succession, making this consistent with automated brute force tooling rather than normal user behavior.

![Splunk dashboard showing failed logon events](pictures/dash1.png)

Drilling into the individual events confirmed that all 15 failed logon attempts originated from the same source IP, `192.168.1.75`, with the workstation name field logged as "Kali", directly identifying the attacking machine. In a real-world scenario an attacker would likely obfuscate this, but regardless of the workstation name field, a single source IP generating 15 consecutive failed logons in rapid succession is sufficient to identify this as a brute force attack. As seen in the screenshot below, the event details clearly show the source IP and workstation name attributed to all 15 attempts.

![Event detail showing source IP and workstation](pictures/attackattempt.png)

| Field | Value |
|---|---|
| Source IP | 192.168.1.75 |
| Workstation Name | Kali |
| Event ID | 4625 (Failed Logon) |
| Attempt Count | 15 |

---

## IOC Summary

| IOC Type | Value | Confidence |
|---|---|---|
| Attacker IP | 192.168.1.75 | High |
| Attacker Workstation | Kali | High |
| Event Signature | 15x Event ID 4625 | High |

---

## MITRE ATT&CK

| Technique ID | Technique Name | Notes |
|---|---|---|
| T1110 | Brute Force | 15 failed logon attempts from single IP |
| T1078 | Valid Accounts | Targeting existing accounts |
| T1021 | Remote Services | Remote connection attempt to Windows host |

---

## Remediation

- **Blocked `192.168.1.75` at the host firewall** - an inbound firewall rule was created on the Windows host to drop all traffic from the attacker's IP, preventing any further connection attempts from that machine.
- **Implemented an account lockout policy** - Group Policy was updated to enforce an account lockout threshold of 5 failed attempts within a defined window. This limits the effectiveness of future brute force attempts by locking the targeted account before a significant number of password guesses can be made.
- **Reset the targeted account's password** - the password of the account targeted during the attack was reset as a precaution. While none of the 15 attempts resulted in a successful logon (a successful authentication would have generated Event ID 4624 rather than 4625), resetting the credential eliminates any residual risk in the event the password was weak or if any attempts went undetected prior to Splunk flagging the activity.

---

*Write-up by Trystan Ruiz*
