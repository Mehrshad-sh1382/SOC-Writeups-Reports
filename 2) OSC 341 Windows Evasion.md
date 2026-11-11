# OSC 341 – Windows Evasion
## Incident Report: RDP Brute-Force Compromise & Windows Defender Bypass

**Hostname:** Elenora  
**Internal IP:** 172.16.17.126  
**Date of Incident:** 12 September 2024  
**Severity:** High  
**Status:** Confirmed Compromise

---

## 1. Executive Summary

On 12 September 2024, the Windows host **Elenora** (172.16.17.126) was successfully compromised via Remote Desktop Protocol (RDP). Following eight failed logon attempts, an attacker from the malicious IP address **89.187.177.73** (reported 154 times on AbuseIPDB; hosted by Datacamp, New York, USA) gained interactive remote access (Logon Type 10 – RemoteInteractive).

Immediately after authentication, the attacker performed reconnaissance of security controls using PowerShell and then executed a publicly documented Windows Defender detection bypass (Exploit-DB 51802) leveraging `rundll32.exe` and VBScript/mshtml to launch `calc.exe` as a proof-of-concept.

---

## 2. Incident Details

| Field              | Value                                              |
|--------------------|----------------------------------------------------|
| Hostname           | Elenora                                            |
| Internal IP        | 172.16.17.126                                      |
| Date / Time        | 12 September 2024, ~08:06 – 08:09 (local time)     |
| Attacker IP        | 89.187.177.73 (Datacamp, New York, USA)            |
| Reputation         | 154 reports on AbuseIPDB (malicious)               |
| Access Method      | RDP (Logon Type 10 – RemoteInteractive)            |
| Alert Type         | Malware / Defense Evasion                          |
| EDR Action         | Not Detected                                       |
| Trigger Process    | C:\Windows\System32\rundll32.exe                   |

---

## 3. Attack Timeline

| Time     | Event ID | Description                                                                 |
|----------|----------|-----------------------------------------------------------------------------|
| 08:06:59 | 4625     | Failed logon (0xC000006A – username correct, password wrong) × 8            |
| 08:07:02 | 4624     | Successful RDP logon (Type 10) from 89.187.177.73                           |
| 08:08:49 | 4104     | PowerShell: `Get-Service -Name WinDefend`                                   |
| 08:08:56 | 4104     | PowerShell: `Get-MpPreference \| Select-Object -Property DisableRealtimeMonitoring` |
| 08:09:03 | 4104     | PowerShell: `Get-NetFirewallProfile`                                        |
| 08:09:03 | 4104     | Additional recon: running services, TCP connections, user accounts, `net user`, `whoami /all` |
| 08:09:35 | 4688     | `rundll32.exe` launched with Defender bypass PoC (LoL45 variant) executing `calc.exe` |

---

## 4. Technical Analysis

### Initial Access
The attacker performed a short password-guessing campaign against an RDP-exposed account. After eight failures, a successful interactive logon occurred from a known malicious IP.

### Discovery / Reconnaissance
Post-authentication PowerShell commands focused on security posture (Defender service, real-time protection, firewall) and situational awareness (users, network connections, privileges).

### Defense Evasion
The attacker executed a known `rundll32` + VBScript + mshtml technique (Exploit-DB 51802). This Living-off-the-Land technique abuses the trusted binary `rundll32.exe` to execute arbitrary VBScript via `mshtml.dll`. The “LoL45” path segment was used to bypass the then-existing Trojan:Win32/Powessere.G detection. Launching `calc.exe` confirmed the evasion worked.

**Trigger Command:**
```cmd
rundll32 vbscript:"\..\mshtml\..\LoL45\..\mshtml,RunHTMLApplication " + String(CreateObject("Wscript.Shell").Run("calc.exe"), 0)
```

---

## 5. MITRE ATT&CK Mapping

| Tactic              | Technique                                      | ID         |
|---------------------|------------------------------------------------|------------|
| Initial Access      | Valid Accounts                                 | T1078      |
| Initial Access      | External Remote Services                       | T1133      |
| Credential Access   | Brute Force                                    | T1110      |
| Execution           | PowerShell                                     | T1059.001  |
| Execution           | Windows Command Shell                          | T1059.003  |
| Defense Evasion     | Exploitation for Defense Evasion               | T1211      |
| Defense Evasion     | System Binary Proxy Execution: Rundll32        | T1218.011  |

---

## 6. Conclusion

This was a successful credential-based RDP compromise followed by reconnaissance and a deliberate Windows Defender evasion attempt using a public LOLBin technique. The execution of `calc.exe` via the bypass indicates the technique was effective at the time of the incident.

---

## 7. Recommendations

- Reset compromised account passwords and restrict RDP access (VPN / IP allow-list + NLA).
- Hunt for persistence and perform full forensic analysis.
- Enable and monitor PowerShell Script Block Logging (4104) and%PDF-1.4
