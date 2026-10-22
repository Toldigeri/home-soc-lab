# Incident Report: Phishing Campaign Simulation

**Date:** 2026-10-20 / 2026-10-22
**Analyst:** Gergő Toldi
**Severity:** High

## Summary
A simulated phishing campaign was conducted using GoPhish, targeting
a fake "SOC Lab Corp" employee login page. The attack chain was
monitored and partially detected through Sysmon network connection
logging in Splunk.

## Attack Chain
1. Phishing email sent via GoPhish (SMTP relay: smtp4dev)
2. Email received and opened on TARGET-PC (via smtp4dev web interface)
3. Embedded link clicked, redirecting to a fake credential-harvesting
   landing page hosted on the attacker machine (Kali, 10.10.10.4)
4. Credentials submitted by the simulated victim and captured by GoPhish

## Detection
Sysmon Event ID 3 (Network Connection) logged the outbound connection
from the victim's browser to the attacker-controlled landing page:

- **Process:** msedge.exe
- **Source:** 10.10.10.5 (TARGET-PC)
- **Destination:** 10.10.10.4:80 (Kali / GoPhish)
- **User:** TARGET-PC\labuser
- **Time (UTC):** 2026-10-22 11:07:14

## Detection Gap Identified
The default SwiftOnSecurity Sysmon configuration excludes private/
internal IP ranges from NetworkConnect logging by default, as it is
optimized for detecting outbound connections to external/public IPs
in enterprise environments. Since the lab network (10.10.10.0/24) is
itself a private range, this connection was NOT visible until the
Sysmon configuration was manually modified to include an explicit
rule for the lab subnet.

## Detection Query (Splunk/SPL)
index=* source="Sysmon" EventCode=3 DestinationIp="10.10.10.4"


## MITRE ATT&CK Mapping
- T1566.002 – Phishing: Spearphishing Link
- T1204.001 – User Execution: Malicious Link

## Recommendations
- In a real environment, deploy DNS and/or proxy logging to detect
  phishing landing pages, since relying solely on endpoint network
  logs creates blind spots for encrypted/external traffic
- Implement email security gateway with URL rewriting/sandboxing
- Conduct regular phishing awareness training for employees
- Review and customize default Sysmon configurations to match the
  specific network architecture being monitored, rather than relying
  on out-of-the-box enterprise-oriented defaults
