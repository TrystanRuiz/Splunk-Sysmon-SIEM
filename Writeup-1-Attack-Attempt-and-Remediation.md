# Splunk SIEM Lab #01 — Brute Force Attack Detection & Remediation
**Platform:** Splunk Enterprise (Home Lab / Sysmon SIEM)
**Category:** Credential Access / Brute Force

---

## Overview

This lab involved detecting and responding to a brute force attack against an internal Windows host. A Kali Linux machine simulated an attacker attempting unauthorized access by repeatedly submitting login credentials. The Splunk SIEM, ingesting Sysmon and Windows Security event logs, detected the activity through a spike in failed logon events and surfaced the attacker's IP address, workstation name, and logon attempt count — providing enough context to investigate and remediate.

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

A Kali Linux machine was used to simulate an attacker attempting to connect to a target Windows host and access files. This represents a real-world brute force or credential stuffing scenario where an adversary attempts to gain unauthorized access through repeated authentication attempts.

![Kali Linux attacker machine simulating a connection attempt against the target host](pictures/attackattemptkali.png)
*Figure 1: Attacker simulation — Kali Linux initiating connection attempts against the target Windows host*

---

### Step 2 — SIEM Detection via Splunk Dashboard

The Splunk dashboard flagged the activity through the **Failed Logons** panel, which monitors Windows Security Event ID 4625. The dashboard surfaced **15 failed logon attempts** within a short timeframe — a clear indicator of automated or repeated credential attack behavior.

![Splunk SOC dashboard highlighting the Failed Logons panel with 15 Event 4625 entries](pictures/dash1.png)
*Figure 2: Splunk dashboard — Failed Logons section showing 15 Event ID 4625 entries triggered by the attack*

---

### Step 3 — IOC Isolation & Log Analysis

Drilling into the event log data, Splunk isolated the key indicators needed for remediation. Each of the 15 failed logon events shared the same source, confirming this was a single attacker making repeated attempts rather than distributed noise.

![Splunk log entry isolating the malicious IP 192.168.1.75 and attacker workstation name Kali](pictures/attackattempt.png)
*Figure 3: Splunk event detail — malicious IP 192.168.1.75 and attacker workstation "Kali" identified*

**Key findings from log analysis:**

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
**Escalation Required:** No — remediated at host level

The Splunk SIEM correctly identified and surfaced a brute force credential attack originating from `192.168.1.75` (Kali). The 15 failed logon events confirmed automated or repeated attack behavior targeting a Windows account. No successful logon was observed, meaning the attack did not result in a breach — however, remediation was applied to prevent future attempts.

**Remediation actions taken:**

- **IP Blocking** — Blocked `192.168.1.75` using the host-based firewall, preventing further authentication attempts at the network layer from this source
- **Account Lockout Policy** — Configured a lockout policy to trigger after 5 failed attempts, which would have contained this attack automatically and defends against future brute force and password spraying
- **Account-Based Remediation** — Forced a password reset on the targeted account and replaced it with a complex credential to reduce further risk and exposure

---

## Key Takeaways

- **Event ID 4625 is a primary brute force indicator** — a spike in failed logons from a single source IP is one of the clearest signals of credential attack activity in Windows environments
- **Splunk dashboards enable rapid triage** — having a dedicated Failed Logons panel surfaced the attacker IP, workstation name, and attempt count in a single view, cutting investigation time significantly
- **Layered remediation is more effective than a single control** — combining IP blocking, lockout policy, and credential rotation addresses the immediate threat, the attack vector, and the exposed account simultaneously
- **A blocked attack still requires investigation** — no successful logon doesn't mean no risk; the targeted account and any adjacent accounts should be reviewed for exposure

---

*Write-up by Trystan Ruiz*
