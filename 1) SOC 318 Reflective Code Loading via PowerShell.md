# SOC 318: Reflective Code Loading via PowerShell (WinPwn / Mimikatz)

## Incident Replay Animation

[![Watch the attack chain animation](https://raw.githubusercontent.com/Mehrshad-sh1382/SOC-Writeups-Reports/main/SOC318_animation_v2.mp4)](https://github.com/Mehrshad-sh1382/SOC-Writeups-Reports/blob/main/SOC318_animation_v2.mp4)

> **Tip:** Click the video above or [open it directly](./SOC318_animation_v2.mp4) to play the full 20-second incident replay.

## Full PDF Report

[**Download / View PDF Report**](./SOC%20318%20-%20Reflective%20Code%20Loading%20Attempt%20via%20PowerShell.pdf)

---

**Hostname:** Lorna  
**Internal IP:** 172.16.17.119  
**Alert Time:** 2024-09-03 12:42:51 +03:00  
**Severity:** High  
**Verdict:** True Positive  
**Author:** Amirali Sheikhan  
**Report Date:** 2024-09-03  

## 1. Executive Summary

On 3 September 2024, the SOC received an alert for Reflective Code Loading via PowerShell on host **Lorna**. Investigation confirmed a successful RDP brute-force attack followed by fileless execution of the WinPwn post-exploitation framework, which reflectively loaded Mimikatz into memory. The endpoint was not isolated.

## 2. Timeline

| Time | Event |
|------|-------|
| 13:40:08 | 17 failed login attempts (brute-force) |
| 13:40:30 | Successful RemoteInteractive (Type 10) logon from **212.102.51.45** |
| 13:42:23 | powershell.exe (PID 4592) created by runas.exe |
| 13:42:44 | ping google.com (connectivity check) |
| 13:42:51 | DNS query + download of WinPwn.ps1 from raw.githubusercontent.com |
| 13:42:51 | Execution of WinPwn download cradle + `mimiload -consoleoutput -noninteractive` |
| 13:42:51 | Network connection to GitHub CDN (185.199.108.133) |

## 3. Attack Chain

1. **Initial Access** – RDP brute-force (T1110) leading to valid account logon (T1078, T1133).
2. **Privilege Context** – `runas /user:LetsDefend "powershell.exe"` (T1548.002).
3. **Fileless Execution** – Download cradle with `iex` + reflective loading of Mimikatz via WinPwn `mimiload` (T1059.001, T1620).
4. **Reconnaissance** – `whoami`, `systeminfo`, `tasklist`, `net user`, `net group`.

## 4. MITRE ATT&CK

- T1110 – Brute Force
- T1078 – Valid Accounts
- T1133 – External Remote Services
- T1059.001 – PowerShell
- T1548.002 – Abuse Elevation Control Mechanism
- **T1620 – Reflective Code Loading** (primary)
- T1071 – Application Layer Protocol

## 5. Indicators of Compromise

- **Attacker IP:** 212.102.51.45
- **Download IP:** 185.199.108.133 (GitHub CDN)
- **URL:** `https://raw.githubusercontent.com/S3cur3Th1sSh1t/WinPwn/121dcee26a7aca368821563cbe92b2b5638c5773/WinPwn.ps1`
- **Command:** `mimiload -consoleoutput -noninteractive`
- **Process:** powershell.exe parented by runas.exe (PID 4592)

## 6. Recommendations

- Isolate host Lorna immediately.
- Reset credentials for the LetsDefend account and related privileged accounts.
- Terminate active RDP sessions.
- Review LSASS access logs for credential dumping.
- Block source IP 212.102.51.45.
- Hunt for similar WinPwn / mimiload patterns across the environment.
- Enforce MFA and lockout policies on RDP.

## 7. Conclusion

Confirmed **True Positive – High Severity**. The attacker achieved fileless reflective code loading of Mimikatz after successful RDP brute-force.

---

Prepared by Amirali Sheikhan – Incident Responder
