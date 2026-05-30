# Incident Report 001 — CIS Benchmark Compliance Failure

**Date:** May 30, 2026 @ 15:45:48
**Agent:** Windows_Agent (ID: 001)
**Agent IP:** 10.0.2.3
**Rule ID:** 19005
**Rule Level:** 9 (Medium)
**SIEM:** Wazuh v4.x

---

## Alert Summary

Wazuh's Security Configuration Assessment (SCA) module completed a scan of the Windows 10 endpoint against the CIS Microsoft Windows 10 Enterprise Benchmark v4.0.0 and returned a compliance score of **27 out of 100**, triggering a rule 19005 alert for scoring below the 30% threshold.

---

## Event Detail

| Field | Value |
|---|---|
| Timestamp | May 30, 2026 @ 15:45:48 |
| Policy | CIS Microsoft Windows 10 Enterprise Benchmark v4.0.0 |
| Policy ID | cis_win10_enterprise |
| SCA Score | 27 |
| Total Checks | 424 |
| Passed | 116 |
| Failed | 304 |
| Invalid | 4 |
| Rule ID | 19005 |
| Rule Level | 9 |
| GDPR | IV_35.7.d |
| NIST-800-53 | CM.1 |
| PCI DSS | 2.2 |
| TSC | CC7.1, CC7.2 |

---

## Analysis

The SCA scan ran 424 checks against the CIS Windows 10 Enterprise Benchmark and failed 304 of them, giving a pass rate of just 27%. This is a significant compliance gap. The CIS Benchmark covers areas including account policy, audit policy, Windows Defender configuration, network settings, and application control.

A score this low on a fresh Windows 10 installation is not unusual — Windows out of the box is not configured to enterprise security standards. However in a real business environment, deploying an endpoint with a 27% CIS score would represent a meaningful risk. The number of failed checks gives an attacker a wide surface area to work with, from weak audit logging to permissive application execution policies.

The alert maps to several compliance frameworks including NIST-800-53 CM.1 (Configuration Management) and PCI DSS 2.2 (System Configuration Standards), indicating this finding would be reportable under both frameworks in a production context.

---

## Recommended Response

In a real SOC environment the recommended actions would be:

- Review the full SCA report in Wazuh to identify the highest-weighted failed checks
- Prioritise remediation of failed checks in the Audit Policy and Account Policy categories first, as these directly impact detection capability
- Apply a CIS-hardened Group Policy baseline to bring the score above the 70% threshold
- Re-run the SCA scan post-remediation to confirm improvement
- Flag the endpoint as non-compliant in the asset register until remediation is complete

---

## Compliance Mapping

| Framework | Control | Relevance |
|---|---|---|
| NIST-800-53 | CM.1 | Configuration management policy and procedures |
| PCI DSS | 2.2 | Develop configuration standards for all system components |
| GDPR | IV_35.7.d | Data protection by design and by default |
| TSC | CC7.1, CC7.2 | System monitoring and anomaly detection |
