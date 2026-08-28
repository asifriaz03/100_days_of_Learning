# Day Log — August 28, 2026

## Topic: Windows Logs, Event Logs & Log Monitoring

---

### What I Learned

Today's focus was Windows logging — how Windows records system activity, where those records live, and how defenders use them to detect and investigate incidents.

#### 1. What Windows Logs Actually Are
Windows keeps a continuous record of everything happening on a system — logins, process launches, service changes, errors, security events — in a binary format called **`.evtx`** files. These are viewable through the **Event Viewer** (`eventvwr.msc`) or queried via PowerShell (`Get-WinEvent`, `Get-EventLog`).

#### 2. The Core Log Categories
Windows organizes logs into several built-in channels:

- **Application** — events logged by installed software (crashes, warnings, install/uninstall activity)
- **Security** — the most important log for security work: logon/logoff events, privilege use, object access, policy changes
- **System** — OS-level events: driver failures, service start/stop, hardware issues
- **Setup** — events related to OS installation and updates
- **Forwarded Events** — logs collected from other machines via Windows Event Forwarding (WEF)

#### 3. Event Structure
Every event has:
- **Event ID** — a numeric code identifying the event type (e.g. `4624` = successful logon)
- **Level** — Information, Warning, Error, Critical
- **Source** — which component/service generated it
- **Timestamp, Task Category, Keywords** — metadata for filtering
- **XML/EventData** — the actual payload (usernames, process paths, IPs, etc.)

#### 4. Key Security Event IDs
These came up as the "must-know" set for security monitoring:

| Event ID | Meaning |
|---|---|
| 4624 | Successful logon |
| 4625 | Failed logon (brute-force indicator) |
| 4634 | Logoff |
| 4648 | Logon using explicit credentials (lateral movement flag) |
| 4672 | Special privileges assigned (admin logon) |
| 4688 | New process created (execution tracking) |
| 4697 | Service installed (persistence technique) |
| 4720 | User account created |
| 4732 | Member added to security-enabled group |
| 1102 | Audit log cleared (classic anti-forensics red flag) |

#### 5. Log Monitoring Concepts
- **Centralized logging**: individual endpoint logs are useless at scale — logs need to be forwarded to a central place (SIEM) for correlation. Learned about **Windows Event Forwarding (WEF)** and **Windows Event Collector (WEC)** as the native Microsoft mechanism.
- **SIEM tools**: platforms like **Splunk**, **Wazuh**, **Elastic (ELK stack)**, and **Microsoft Sentinel** ingest logs, index them, and let analysts search/alert on patterns (e.g. 10+ failed logons in a minute = potential brute force).
- **Sysmon (System Monitor)**: a free Sysinternals tool that massively extends default Windows logging — captures process creation with full command lines, network connections, file creation, registry changes — far more detail than the native Security log alone. Considered essential for any serious detection setup.
- **Log retention & rotation**: by default, Windows logs have size caps and will overwrite old events — a key reason forwarding logs off the host to long-term storage matters (both for security and for not losing evidence before an attacker clears them).

#### 6. Why This Matters for Security Work
- **Detection**: most attacker behavior leaves a log trail — logons, process execution, privilege escalation. Knowing the Event IDs above means being able to write detection rules.
- **Incident response**: when investigating a compromise, Event Logs are often the first place to reconstruct a timeline (who logged in, what ran, what changed).
- **Anti-forensics awareness**: Event ID 1102 (log cleared) or 104 (log service stopped) are often red flags that logs themselves were tampered with — meaning "what's missing" can be as telling as what's present.

---

### Resources Used
- Windows Event Viewer (hands-on exploration)
- PowerShell `Get-WinEvent` cmdlet
- Notes on Sysmon and centralized log forwarding concepts

### Key Takeaways
- Security-relevant Windows activity is almost always logged *somewhere* — the skill is knowing which Event ID to look for.
- Native Windows logging is a good start, but Sysmon + a SIEM is what real-world monitoring looks like.
- Log tampering (clearing/stopping logs) is itself a detectable event — attackers can't fully erase their tracks without leaving a different trace.

### Problems Faced
- Native Windows Security log can be noisy and lacks command-line detail without Sysmon or Group Policy audit tuning — took some digging to understand why "just enabling logging" isn't enough on its own.

### Tomorrow's Plan
- Set up Sysmon in the home lab with a solid config (e.g. SwiftOnSecurity's Sysmon config) and start correlating Sysmon events with Security log events for the same activity.
