# Splunk + Sysmon Home Lab SIEM

![Splunk](https://img.shields.io/badge/Splunk-Enterprise-black?style=flat-square&logo=splunk)
![Sysmon](https://img.shields.io/badge/Sysmon-v15-blue?style=flat-square&logo=windows)
![MITRE](https://img.shields.io/badge/MITRE-ATT%26CK-red?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Windows_11-0078D6?style=flat-square&logo=windows11)

A hands-on home lab SIEM built with Splunk Enterprise and Sysmon for real-world threat detection and incident response. Each writeup simulates an attack from a Kali Linux machine against a Windows target, with full detection and remediation documented.

---

## Architecture

```
  [ Kali Linux ]  ──── attacks ────►  [ Windows 11 ]  ──── logs ────►  [ Splunk SIEM ]
   (attacker VM)                      (victim host)                    (192.168.1.111)
                                      Sysmon + UF
```

**Log sources forwarded to Splunk:**
- `Microsoft-Windows-Sysmon/Operational` — process, network, registry, and file telemetry
- `WinEventLog:Security` — logon events and account activity
- `Microsoft-Windows-PowerShell/Operational` — script block logging (Event 4104)

---

## Writeups

| # | Title | Key Events | MITRE ATT&CK |
|---|-------|-----------|--------------|
| 1 | [Brute Force Attack Detection & Remediation](Writeup-1-Attack-Attempt-and-Remediation.md) | Event 4625 | [T1110](https://attack.mitre.org/techniques/T1110/) |
| 2 | [Lateral Movement Detection](Writeup-2-Lateral-Movement-Detection.md) | Sysmon 1/3/10, Event 4624 T3, 4648 | [T1021](https://attack.mitre.org/techniques/T1021/) |
| 3 | [Persistence Mechanism Detection](Writeup-3-Persistence-Mechanism-Detection.md) | Sysmon 12/13, Event 4698 | [T1053](https://attack.mitre.org/techniques/T1053/) / [T1547](https://attack.mitre.org/techniques/T1547/) |
| 4 | [PowerShell Abuse Detection](Writeup-4-PowerShell-Abuse-Detection.md) | Event 4104, Sysmon 1/3 | [T1059.001](https://attack.mitre.org/techniques/T1059/001/) |
| 5 | [C2 Beacon Detection](Writeup-5-C2-Beacon-Detection.md) | Sysmon 3, Sysmon 1 | [T1071](https://attack.mitre.org/techniques/T1071/) |

---

## Tech Stack

| Component | Tool |
|-----------|------|
| SIEM | Splunk Enterprise |
| Endpoint Telemetry | Sysmon (Microsoft Sysinternals) |
| Log Forwarding | Splunk Universal Forwarder |
| Attacker | Kali Linux |
| Victim | Windows 11 |
| Hypervisor | Proxmox VE |

---

## Setup & Configuration

See [SplunkOverview.md](SplunkOverview.md) for full deployment details.

---

*By [Trystan Ruiz](https://trystanruiz.tech) | [LinkedIn](https://linkedin.com/in/trystanruiz)*
