# Incident Report: Encoded PowerShell Execution

**Date:** 2026-10-18
**Analyst:** Gergő Toldi
**Severity:** Medium

## Summary
Simulated execution of a Base64-encoded PowerShell command on the
Windows endpoint, detected via Sysmon logs forwarded to Splunk.

## Timeline
| Time | Event |
|---|---|
| 12:20:52 | Encoded PowerShell command executed on TARGET-PC |
| ~12:21 - 12:22 | Event ingested into Splunk (Sysmon EventCode=1) |

## Details
- **Host:** TARGET-PC
- **User:** TARGET-PC\labuser (Medium integrity, non-elevated)
- **Parent process:** powershell.exe (PID 7436)
- **Child process:** powershell.exe (PID 5296), -EncodedCommand
- **Process hash (SHA256):** 64DD55E1C2373DEED25C2776F553C632E58C45E56A0E4639DFD54EE97EAB9C19
- **Decoded command:** `Write-Host 'Simulated suspicious activity - gathering host info'; Get-ComputerInfo | Select-Object CsName, OsVersion`

## Detection Query (Splunk/SPL)
index=* source="*Sysmon*" EventCode=1 CommandLine="*EncodedCommand*"


## MITRE ATT&CK Mapping
- T1059.001 – Command and Scripting Interpreter: PowerShell
- T1027 – Obfuscated Files or Information (Base64 encoding)

## Recommendations
- Enable PowerShell Script Block Logging for automatic decoding of
  encoded commands at the source
- Create a Splunk alert for any process containing "-EncodedCommand"
  or "-enc" in the command line
- Investigate why a PowerShell process spawned another PowerShell
  process (parent-child PowerShell chains are a common indicator of
  scripted or automated execution)
