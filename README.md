# SOC Home Lab

A self-contained SOC investigation lab: deploy a Windows victim VM with
Sysmon + Splunk logging, simulate an RDP brute-force intrusion, investigate
the resulting logs like a SOC analyst, and document findings in a real
incident ticket format.

## Goal
Produce hands-on, verifiable evidence of log investigation and incident
documentation — not just tool familiarity.

## Stack
- **Hypervisor:** VMware Workstation Pro
- **Victim:** Windows 11 (VM), Sysmon (SwiftOnSecurity config)
- **Attacker:** Kali Linux (VM), Hydra
- **SIEM:** Splunk Enterprise (host) + Universal Forwarder (VM)
- **Network:** Isolated Host-only (VMnet1), no internet/LAN exposure
- **Ticket format:** SANS incident handling template

## Status
In progress — environment and Sysmon logging verified. Splunk setup next.

## Structure
- `docs/` — build plan and incident ticket
- `sysmon/` — Sysmon configuration used
- `splunk/` — SPL queries used in investigation
- `screenshots/` — evidence captured during the investigation phase