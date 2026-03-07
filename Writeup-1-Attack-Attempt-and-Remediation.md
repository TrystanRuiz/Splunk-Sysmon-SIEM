# Splunk SIEM Lab #01 — Brute Force Attack Detection & Remediation

---

## Overview

Brute force attack picked up by my Splunk SIEM. A Kali machine tried to log into a target Windows host repeatedly. Splunk flagged it through failed logon events (Event 4625), I pulled the source IP and workstation name from the logs and applied remediation to lock it down.

---

## Alert Details

| Field | Value |
|---|---|
| Event ID | 4625 |
| Alert Rule | Failed Logon Attempts Detected |
| Incident Type | Brute Force / Credential Access |
| Severity | Medium |
| Data Source | Windows Security Event Log (Sysmon) |
| Action Taken | Investigated & Remediated |

---

## Investigation

### Step 1 — Simulated Attack

Ran a simulation from a Kali Linux machine trying to connect to the target Windows host and access files. This is what a brute force or credential stuffing attempt looks like from the attacker side.

![Kali Linux attacker machine simulating a connection attempt against the target host](pictures/attackattemptkali.png)
*Figure 1: Kali Linux initiating connection attempts against the target Windows host*

---

### Step 2 — SIEM Detection via Splunk Dashboard

Splunk picked it up through the Failed Logons panel. 15 Event 4625 entries all in a short window — that's not a user mistyping their password, that's automated.

![Splunk SOC dashboard highlighting the Failed Logons panel with 15 Event 4625 entries](pictures/dash1.png)
*Figure 2: Splunk dashboard — Failed Logons section showing 15 Event ID 4625 entries*

---

### Step 3 — IOC Isolation & Log Analysis

Drilled into the individual events. Every one of the 15 failed logons came from the same source — `192.168.1.75`, workstation name "Kali". Pretty easy to isolate from there.

![Splunk log entry isolating the malicious IP 192.168.1.75 and attacker workstation name Kali](pictures/attackattempt.png)
*Figure 3: Splunk event detail — malicious IP 192.168.1.75 and attacker workstation "Kali" identified*

| Field | Value | Significance |
|---|---|---|
| Source IP | 192.168.1.75 | Attacker machine — Kali Linux |
| Workstation Name | Kali | Confirms attacker machine identity |
| Event ID | 4625 | Windows failed logon |
| Attempt Count | 15 | Consistent with brute force behavior |
| Direction | Inbound | External-to-internal authentication attempt |

---

## IOC Summary

| IOC Type | Value | Confidence |
|---|---|---|
| Attacker IP | 192.168.1.75 | High |
| Attacker Workstation | Kali | High |
| Event Signature | 15x Event ID 4625 (Failed Logon) | High |

---

## MITRE ATT&CK Mapping

| Technique ID | Technique Name | Observed Behavior |
|---|---|---|
| T1110 | Brute Force | 15 repeated failed logon attempts from a single source IP |
| T1078 | Valid Accounts | Attacker targeting existing user accounts to gain access |
| T1021 | Remote Services | Attacker attempting to connect to target host remotely |

---

## Verdict & Response

**Classification:** True Positive
**Escalation Required:** No — handled at host level

15 failed logons from one IP in a short window. Clear brute force. No successful logon so nothing was accessed, but still needed to remediate.

**Remediation steps:**

- Blocked `192.168.1.75` on the host firewall to cut off any further attempts from that IP
- Set up an account lockout policy — 5 failed attempts triggers a lockout, which would've stopped this automatically
- Reset the targeted account's password with a stronger one to reduce exposure

---

## Key Takeaways

- 15 failed logons from one IP in a short window is an obvious pattern in Splunk. Event 4625 is worth having a dedicated panel for
- The workstation name "Kali" showing up in the logs is a giveaway in a lab environment, but in a real scenario that name would be randomized — the IP and attempt volume are the actual indicators to rely on
- Layering the remediation made sense here: block the IP, add a lockout policy, reset the password. Any one of those alone leaves gaps

---

*Write-up by Trystan Ruiz | Splunk Enterprise Home Lab | Blue Team Portfolio*
