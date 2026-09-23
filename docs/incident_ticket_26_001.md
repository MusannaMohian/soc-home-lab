# Incident Ticket: INC-2026-001

## Summary

On September 22, 2026, an automated brute-force attack against RDP
successfully compromised the local account `svc_backup` on host
DESKTOP-THBTKJ6. The attacker (source: 192.168.138.130) gained an
interactive RDP session and performed local reconnaissance (whoami,
privilege enumeration, user account listing, network configuration)
before making one outbound connection to an internal host on port
8000. No lateral movement or privilege escalation was observed. This
incident was simulated in an isolated home lab for training purposes.

## Detection Source

Splunk saved search: Failed Logon Volume
(`index=main EventCode=4625 | stats count by Account_Name, Source_Network_Address`)

## Severity

**Medium** — successful compromise of a single low-privilege local
account, contained entirely within an isolated lab network with no
production impact.

## Timeline

| Time (local) | Event | Evidence |
|---|---|---|
| 20:20:07–20:20:20 | Brute-force attempt: 5 failed RDP logons against `svc_backup` | EventCode 4625 |
| 20:20:20 | Successful logon — credential compromised | EventCode 4624 |
| 20:37:41–20:37:44 | Interactive RDP session established | EventCode 4624, Logon_Type 10 |
| 20:38:11 | `whoami` executed | Sysmon EID 1 |
| 20:38:22 | `whoami /priv` executed | Sysmon EID 1 |
| 20:38:31 | `net user` executed | Sysmon EID 1 |
| 20:38:44 | `ipconfig /all` executed | Sysmon EID 1 |
| 20:42:25 | PowerShell launched; recon sequence re-run | Sysmon EID 1 |
| 20:45:59 | Outbound connection to 192.168.138.1:8000 | Sysmon EID 3 |

## Account(s) Affected

`svc_backup` (local, standard/non-admin privileges)

## Source IP

192.168.138.130

## Indicators of Compromise

- Source IP: 192.168.138.130
- Target account: svc_backup
- Recon commands: whoami, whoami /priv, net user, ipconfig /all
- Outbound connection: 192.168.138.1:8000 via powershell.exe (Invoke-WebRequest)
- Compromised credential: [REDACTED] (lab environment only)

## Analyst Assessment

**True positive.** Confidence: high. The failed-logon burst, successful
logon, and subsequent process activity all correlate to a single
account and source IP within a tight time window, with no ambiguity
in attribution. `whoami /priv` output confirmed the account held no
elevated privileges, limiting the practical impact of the compromise.

## Response Actions Taken

- Account `svc_backup` password reset (simulated)
- RDP access reviewed; NLA was disabled during testing and should be
  re-enabled in any non-lab deployment

## Recommendations

1. Enforce account lockout after a small number of failed logon
   attempts (this account had none configured)
2. Enable Network Level Authentication (NLA) on all RDP-exposed hosts
3. Require MFA for RDP access, particularly for service-style accounts
4. Restrict RDP exposure to trusted network segments only
5. Alert on failed-logon volume exceeding a defined threshold
   (detection query already built: `EventCode=4625 | stats count by
   Account_Name, Source_Network_Address`)