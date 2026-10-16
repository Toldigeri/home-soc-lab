# Incident Report: SSH Brute Force Attack

**Date:** 2026-10-16
**Analyst:** Gergő Toldi
**Severity:** Medium

## Summary
Simulated SSH brute force attack against the SIEM host using Hydra,
followed by log analysis to identify the attack pattern.

## Timeline
| Time (UTC+2) | Event |
|---|---|
| 11:59:07 | First failed login attempt observed |
| 12:11:15 | Last failed login attempt |
| 12:03:06 | Successful login |

## Details
- **Source IP:** 10.10.10.4 (Kali Linux, attacker)
- **Target IP:** 10.10.10.3 (splunk-siem, Ubuntu Server), port 22/SSH
- **Tool used:** Hydra v9.7
- **Attempts:** 53 failed logins before success

## Observations
- A 16 and 4 thread attack failed due to the SSH server's
  per-connection authentication try limit (PAM MaxAuthTries).
- A single-threaded ("low and slow") attack bypassed this limit
  and succeeded.

## Evidence
See `/var/log/auth.log`:
2026-10-16T11:59:07.415181+00:00 splunk-siem sshd-session[1878]: Failed password for invalid user jack from 10.10.10.4 port 52164 ssh2
2026-10-16T12:11:15.262074+00:00 splunk-siem sshd-session[2206]: Failed password for invalid user brandon from 10.10.10.4 port 57054 ssh2
2026-10-16T12:03:06.174165+00:00 splunk-siem sshd-session[1951]: Accepted password for socadmin from 10.10.10.4 port 53178 ssh2
