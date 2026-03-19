# 🚨 SOC Investigation Case Study — Web Exploitation to Privilege Escalation

**Platform:** TryHackMe
**SIEM Used:** Elastic ELK Stack
**Analyst:** Sarvesh Pandekar

---

## 📌 Scenario Overview

A high-severity alert was triggered indicating suspicious web requests targeting an internal Windows Server (winserv2019.some.corp).

Investigation revealed a complete attack chain involving:

* Web exploitation attempt
* Remote command execution
* Administrator login outside business hours
* New persistence account creation
* Privilege escalation using command-line

---

## 🧠 Alert Timeline

| Time  | Activity                                                |
| ----- | ------------------------------------------------------- |
| 04:38 | Suspicious POST requests (possible file upload exploit) |
| 04:45 | Command execution via ASPX web shell                    |
| 05:11 | Administrator login detected                            |
| 05:13 | New user account created                                |
| 05:13 | Privilege escalation via cmd.exe                        |

---

## 🔎 Investigation Steps

---

### 🚨 Alert 1 — Suspicious File Upload Attempt

WAF detected multiple POST requests to:

```
/ecp/proxyLogon.ecp
```

This indicates potential exploitation attempt similar to **Exchange ProxyLogon vulnerability**.

📷 Alert-1 & Investigation Screenshot

![POST Logs](screenshots/elastic_post_logs.png)

Observations:

* Source IP → 203.0.113.55
* User Agent → python-requests
* HTTP Status → 200 (Request accepted)

✅ Conclusion: Attacker likely attempted to upload web shell.

---

### 🚨 Alert 2 — Command Execution via Web Shell

Multiple GET requests observed:

```
/errorEE.aspx?cmd=
```

Commands executed:

* rundll32 MiniDump (Credential dumping attempt)
* Get-Process lsass (Recon)

📷 Alert-2 & Investigation Screenshot

![Web Shell Commands](screenshots/elastic_get_logs.png)

✅ Conclusion: Web shell successfully deployed and used.

---

### 🚨 Alert 3 — Administrator Login

Windows Event ID detected:

```
4624 — Successful Logon
Logon Type: RemoteInteractive (RDP)
Source IP: 203.0.113.55
```

📷  Alert-3 & Investigation Screenshot

![Admin Login](screenshots/elastic_login_event.png)

✅ Conclusion: Attacker gained admin access.

---

### 🚨 Alert 4 — New Persistence Account Created

Windows Event ID:

```
4720 — User Account Created
Username: svc_backup
```

📷  Alert-4 & Investigation Screenshot

![User Creation](screenshots/elastic_event_4720.png)

✅ Conclusion: Persistence mechanism established.

---

### 🚨 Alert 5 — Privilege Escalation Activity

Command execution observed:

```
net localgroup "Administrators" svc_backup /add
net localgroup "Remote Desktop Users" svc_backup /add
```

Parent Process:

```
cmd.exe
```

📷  Alert-5 & Investigation Screenshot

![Privilege Escalation](screenshots/elastic_cmd_logs.png)

✅ Conclusion: Attacker granted elevated privileges.

---

## 🔗 Attack Chain Mapping

| MITRE Stage          | Activity                |
| -------------------- | ----------------------- |
| Initial Access       | Web Exploit             |
| Execution            | Web Shell Commands      |
| Persistence          | New User Creation       |
| Privilege Escalation | Localgroup Modification |
| Lateral Movement     | RDP Login               |

---

## 🛡️ Recommended Mitigations

* Patch vulnerable Exchange / Web services
* Restrict internet exposure of admin portals
* Monitor Event ID 4720 & 4732
* Enable PowerShell Script Block Logging
* Deploy WAF rules for web shell patterns

---

## ✅ Final Verdict

This was a **successful multi-stage compromise** originating from web exploitation which resulted in full administrative control and persistence on the target server.

---

⭐ If you like this investigation feel free to star the repo.
