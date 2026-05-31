# Home SOC Lab

**Author:** Jacob Pinkerton | **Built:** May 2026 | **Tools:** Wazuh, VirtualBox, Kali Linux, Windows 10

---

## Overview

A home Security Operations Centre built using VirtualBox and Wazuh as the SIEM. The lab consists of three virtual machines — a Wazuh server, a monitored Windows 10 endpoint, and a Kali Linux attack machine — connected via an isolated NAT network.

The goal was to get hands-on with real alert triage, vulnerability detection, and incident documentation in an environment that mirrors how a SOC analyst would actually work, rather than just studying the theory.

---

## Lab Architecture

```
┌─────────────────────────────────────────────┐
│           NAT Network (10.0.2.0/24)         │
│                                             │
│  ┌─────────────┐      ┌─────────────────┐   │
│  │  Kali Linux │      │   Windows 10    │   │
│  │  (Attacker) │────▶│   (Endpoint)    │   │
│  │  10.0.2.x   │      │   10.0.2.3      │   │
│  └─────────────┘      └────────┬────────┘   │
│                                │            │
│                       ┌────────▼────────┐   │
│                       │  Wazuh Server   │   │
│                       │    (SIEM)       │   │
│                       │   10.0.2.5      │   │
│                       └─────────────────┘   │
└─────────────────────────────────────────────┘
```

---

## Tools Used

| Tool | Role |
|---|---|
| Wazuh v4.x | SIEM, vulnerability detection, SCA, threat hunting |
| Windows 10 Home | Monitored endpoint (Wazuh agent installed) |
| Kali Linux | Attack machine for generating alerts |
| VirtualBox | Virtualisation platform |
| Nmap | Network reconnaissance and port scanning |
| Hydra | Credential brute force testing |

---

## What Wazuh Detected

The lab generated real detections across three categories without any manual configuration of custom rules:

**Vulnerability Detection**
Wazuh automatically scanned the Windows 10 endpoint and identified multiple CVEs including a High severity privilege escalation vulnerability (CVE-2026-40398, CVSS 7.8) in Windows Remote Desktop, published May 2026 and unpatched on the endpoint.

**Security Configuration Assessment (SCA)**
Wazuh ran the CIS Microsoft Windows 10 Enterprise Benchmark v4.0.0 against the endpoint and returned a compliance score of 27/100, flagging 304 failed checks across audit policy, account policy, application control, and Windows Defender configuration.

**System Event Monitoring**
Windows application error events were captured and correlated against GDPR and GPG 13 compliance frameworks automatically by Wazuh's ruleset.

_Note: Network reconnaissance and SMB brute force attempts conducted from the Kali VM did not generate Wazuh alerts on this configuration, as Windows 10 Home does not log these activities to the Windows Event Log by default. Kali attack output is documented separately in the Network Reconnaissance section._

---

## Incident Reports

Three incident reports are documented in this repo based on real alerts generated during the lab:

| Report | Finding | Severity | Rule ID |
|---|---|---|---|
| [001 — CIS Benchmark Compliance Failure](incident-reports/001-cis-benchmark-compliance-failure.md) | Windows 10 scored 27% against CIS Enterprise Benchmark | Medium (Level 9) | 19005 |
| [002 — Windows Application Error Event](incident-reports/002-explorer-crash-application-error.md) | Explorer.exe crash, potential process injection indicator | Medium (Level 9) | 60602 |
| [003 — CVE-2026-40398 Privilege Escalation](incident-reports/003-cve-2026-40398.md) | Unpatched heap buffer overflow in Windows Remote Desktop | High (Level 10) | 23505 |

Each report follows a consistent format covering alert summary, event detail, analysis, recommended response, and compliance mapping.

---

## Network Reconnaissance

A port scan was conducted against the Windows 10 endpoint from Kali to demonstrate the difference between a hardened and unhardened host.

**With Windows Firewall enabled:**
All 1000 scanned ports returned as filtered, confirming the firewall was blocking inbound probe traffic effectively.

**With Windows Firewall disabled:**
Three open ports were identified:

| Port | Service | Notes |
|---|---|---|
| 135/tcp | Microsoft RPC | Remote procedure call, lateral movement target |
| 139/tcp | NetBIOS-SSN | Legacy Windows networking, enumeration risk |
| 445/tcp | Microsoft SMB | File sharing, brute force and relay attack surface |

This demonstrates the practical value of host-based firewalls and directly relates to the SMB Signing vulnerability documented in the [Vulnerability Assessment Report](https://github.com/Jpinkerton-Sec/CyberSec-Portfolio).

---

## Key Takeaways

A default Windows 10 Home installation is significantly under-hardened out of the box. A 27% CIS benchmark score, multiple unpatched CVEs, and three exposed network services on a freshly deployed endpoint illustrates why baseline hardening and continuous vulnerability management are foundational to any security programme.

From a SOC perspective, the most valuable part of this lab was learning to triage alerts in context rather than in isolation. The explorer.exe crash (Report 002) is a good example — on its own it looks like a stability issue, but in a real environment it would warrant a quick check for process injection indicators before being closed.

---

## Repository Structure

```
home-soc-lab/
├── README.md
└── incident-reports/
    ├── 001-cis-benchmark-compliance-failure.md
    ├── 002-explorer-crash-application-error.md
    └── 003-cve-2026-40398.md
```

---

*All IP addresses reflect an isolated virtual lab environment. No production systems were involved.*
