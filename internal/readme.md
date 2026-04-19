Done. You’ve fully compromised the machine end-to-end. Now let’s turn everything you did into a **clean, professional penetration testing report** you can submit or even show in interviews.

---

# 📄 Penetration Testing Report

**Target:** Internal (TryHackMe Lab)
**Tester:** Faiq
**Date:** April 2026

---

# 🧾 Executive Summary

A full-scope penetration test was conducted against the target system. The assessment identified multiple critical vulnerabilities that allowed complete system compromise, starting from external web access and ending with root-level control.

The attack chain involved weak credentials, insecure configurations, exposed services, and poor credential management practices. Exploitation of these issues resulted in unauthorized access to sensitive systems, including administrative services and root privileges.

---

# 🎯 Scope

* Target: `internal.thm`
* Environment: Simulated internal infrastructure
* Objective: Gain root access and identify vulnerabilities

---

# 🧪 Methodology

Testing followed a standard penetration testing approach:

1. Reconnaissance
2. Enumeration
3. Exploitation
4. Privilege Escalation
5. Post-Exploitation

---

# 🔍 Detailed Findings

---

## 🟠 1. Open Ports & Service Enumeration

### 📌 Finding

* Port 22 → SSH
* Port 80 → Apache Web Server

### 📸 Evidence

```

```

### ⚠️ Risk

* Public exposure of services increases attack surface

---

## 🔴 2. Directory Enumeration Revealed Sensitive Endpoints

### 📌 Finding

* `/blog` → WordPress
* `/phpmyadmin` → Database interface

### 📸 Evidence

```
[Insert Screenshot: feroxbuster results]
```

### ⚠️ Risk

* Exposed admin panels enable targeted attacks

---

## 🔴 3. Weak WordPress Credentials (Brute Force)

### 📌 Finding

* Username: admin
* Password: my2boys

### 📸 Evidence

```
[Insert Screenshot: WPScan successful login]
```

### ⚠️ Risk

* Weak password allowed admin takeover

---

## 🔴 4. Remote Code Execution via Theme Editor

### 📌 Finding

* PHP reverse shell uploaded via WordPress theme editor

### 📸 Evidence

```
[Insert Screenshot: reverse shell connection]
```

### ⚠️ Risk

* Full server command execution as `www-data`

---

## 🔴 5. Credential Exposure in File

### 📌 Finding

File:

```
/opt/wp-save.txt
```

Credentials:

```
aubreanna : bubb13guM!@#123
```

### 📸 Evidence

```
[Insert Screenshot: wp-save.txt contents]
```

### ⚠️ Risk

* Plaintext credentials → lateral movement

---

## 🔴 6. SSH Access via Credential Reuse

### 📌 Finding

* Successful login as `aubreanna`

### 📸 Evidence

```
[Insert Screenshot: SSH login success]
```

---

## 🔴 7. Internal Service Discovery (Jenkins)

### 📌 Finding

* Jenkins running on:

```
172.17.0.2:8080
```

### 📸 Evidence

```
[Insert Screenshot: Jenkins access via curl]
```

### ⚠️ Risk

* Internal service exposed to compromised user

---

## 🔴 8. Weak Jenkins Credentials

### 📌 Finding

* Username: admin
* Password: spongebob

### 📸 Evidence

```
[Insert Screenshot: Hydra success]
```

### ⚠️ Risk

* No brute-force protection

---

## 🔴 9. Command Execution via Jenkins Script Console

### 📌 Finding

* Groovy script execution allowed OS commands

### 📸 Evidence

```
[Insert Screenshot: whoami output in Jenkins]
```

### ⚠️ Risk

* Direct system command execution

---

## 🔴 10. Sensitive Data Exposure in Container

### 📌 Finding

File:

```
/opt/note.txt
```

Credentials:

```
root : tr0ub13guM!@#123
```

### 📸 Evidence

```
[Insert Screenshot: note.txt contents]
```

### ⚠️ Risk

* Root credentials exposed inside container

---

## 🔴 11. Root Compromise

### 📌 Finding

* Full root access achieved via SSH

### 📸 Evidence

```
[Insert Screenshot: root.txt flag]
```

---

# 💀 Final Proof of Compromise

```
THM{d0ck3r_d3str0y3r}
```

---

# 📊 Risk Summary

| Vulnerability             | Severity |
| ------------------------- | -------- |
| Weak passwords            | Critical |
| Credential reuse          | Critical |
| Plaintext secrets         | Critical |
| Internal service exposure | High     |
| Jenkins misconfiguration  | Critical |

---

# 🛡️ Recommendations

### 🔐 Authentication

* Enforce strong password policies
* Implement MFA

### 🔒 Credential Management

* Never store credentials in plaintext
* Use secrets managers

### 🌐 Network Security

* Restrict internal services (Jenkins)
* Implement network segmentation

### ⚙️ Application Security

* Disable WordPress file editor
* Keep software updated

### 🧱 Hardening

* Disable unnecessary services
* Apply least privilege principle

---

# 🧠 Key Takeaways

* Small misconfigurations chain into full compromise
* Internal services are high-value targets
* Credential reuse is extremely dangerous

---

# 🏁 Conclusion

The system was **fully compromised from external access to root level** due to multiple critical vulnerabilities. Immediate remediation is required to prevent real-world exploitation.

---
