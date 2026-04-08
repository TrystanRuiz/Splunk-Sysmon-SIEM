# Splunk + Sysmon Home Lab SIEM

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

| # | Title |
|---|-------|
| 1 | [Brute Force Attack Detection & Remediation](Writeup-1-Attack-Attempt-and-Remediation.md) |
| 2 | [Lateral Movement Detection](Writeup-2-Lateral-Movement-Detection.md) |
| 3 | [Persistence Mechanism Detection](Writeup-3-Persistence-Mechanism-Detection.md) |
| 4 | [PowerShell Abuse Detection](Writeup-4-PowerShell-Abuse-Detection.md) |
| 5 | [C2 Beacon Detection](Writeup-5-C2-Beacon-Detection.md) |

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
