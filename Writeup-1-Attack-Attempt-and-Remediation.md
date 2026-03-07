# Splunk SIEM Lab #01 — Brute Force Attack Detection & Remediation

---

## Overview

Ran a brute force simulation on my home lab — Kali Linux as the attacker, Windows host as the target. Wanted to see how my Splunk setup would pick it up and what the logs actually look like from the defender side.

---

## The Attack

Set up Kali to repeatedly attempt logins against the Windows machine. From the attacker side it looks like this:

![Kali Linux initiating connection attempts against the target host](pictures/attackattemptkali.png)

---

## Detection

Splunk caught it through the Failed Logons panel on the dashboard. 15 Event ID 4625 entries back to back — that pattern doesn't look like someone forgetting their password, it looks like something automated running through credentials.

![Splunk dashboard showing 15 failed logon events](pictures/dash1.png)

Drilled into the events. Every single one came from `192.168.1.75`, workstation name "Kali". In a real environment the machine name wouldn't be that obvious but the IP and the volume of attempts are the actual indicators.

![Individual event showing source IP 192.168.1.75 and workstation Kali](pictures/attackattempt.png)

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
| T1110 | Brute Force | Repeated failed logon attempts from single IP |
| T1078 | Valid Accounts | Targeting existing accounts to gain access |
| T1021 | Remote Services | Remote connection attempt to Windows host |

---

## Remediation

Three things to lock it down:

1. **Blocked the IP** — added `192.168.1.75` to the host firewall rules. Cuts off that source immediately.
2. **Account lockout policy** — set to trigger after 5 failed attempts. Would have stopped this attack at attempt 5 instead of letting it run to 15.
3. **Password reset** — reset the targeted account with a stronger password just in case.

No single fix is enough on its own. The IP block helps now but a real attacker would rotate IPs. The lockout policy is the one that actually scales.

---

*Write-up by Trystan Ruiz | Splunk Enterprise Home Lab*
