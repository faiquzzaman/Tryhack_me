
# TryHackMe — PyRat (Detailed Write-up)

## 📌 Overview

The PyRat challenge involved exploiting a custom TCP service that evaluates user input as Python code. The attack chain included:

- Service misconfiguration & protocol mismatch
- Arbitrary code execution
- Reverse shell access
- Credential leakage via Git repository
- Source code recovery using Git forensics
- Abuse of hidden admin functionality
- Privilege escalation to root

---

## 🎯 Objectives

- Gain initial access
- Pivot between users
- Identify misconfigurations
- Escalate privileges to root

---

## 🔍 Reconnaissance

### Host Mapping

```bash
echo "<target_ip> pyrat.thm" | sudo tee -a /etc/hosts
````

### Full Port Scan

```bash
nmap -p- -sC -sV -oN pyrat.txt pyrat.thm
```

### Results

| Port | Service  | Notes                                    |
| ---- | -------- | ---------------------------------------- |
| 22   | SSH      | Standard service, not targeted initially |
| 8000 | HTTP-alt | Unusual Python-based service             |

---

## 🌐 Service Enumeration (Port 8000)

### Initial Observation

* Browser access → failed
* Indicates:

  * Not a standard HTTP service
  * Possible custom protocol

### TCP Interaction

```bash
nc pyrat.thm 8000
```

### Behavior Analysis

| Input      | Output      |
| ---------- | ----------- |
| whoami     | not defined |
| uname      | not defined |
| print("x") | x           |

### 🧠 Key Insight

The service evaluates input as **Python code execution**.

---

## 💥 Exploitation — Code Execution to Shell

### Step 1: Setup Listener

```bash
nc -lvnp 4444
```

### Step 2: Trigger Reverse Shell

* Used code execution to spawn a shell
* Established reverse connection

### Result

```bash
whoami
www-data
```

➡️ Initial access gained as low-privileged web user

---

## 🔎 Post-Exploitation Enumeration

### System Recon

```bash
id
uname -a
ls -la
```

### Key Discovery

```bash
/opt/dev/.git
```

➡️ Presence of Git repository on production system

---

## 🔐 Credential Leakage via Git

### Inspect Config

```bash
cat /opt/dev/.git/config
```

### Finding

* Cached credentials for another user
* Indicates:

  * Poor credential hygiene
  * Sensitive data stored in repo config

---

## 🔄 User Pivot

```bash
su <user>
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

### Result

```bash
whoami
think
```

➡️ Privilege escalation from service account → user account

---

## 📁 User Enumeration

```bash
ls -la /home/think
```

### Finding

* Located `user.txt` (flag redacted)

---

## 🧠 Git Forensics & Source Code Recovery

### Step 1: Analyze Git Index

```bash
cat .git/index
```

* Revealed deleted file:

```
pyrat.py.old
```

### Step 2: Restore File

```bash
git restore pyrat.py.old
```

---

## 🧬 Source Code Analysis

### Key Functionalities Identified

* `exec_python()` → executes user input
* `shell()` → spawns `/bin/sh` via socket
* Weak admin validation logic

### Critical Code Insight

```python
if data == 'shell':
    shell(client_socket)
else:
    exec_python(client_socket, data)
```

### 🧠 Security Issue

* No proper authentication before executing sensitive actions
* Trusts client input blindly
* Weak admin check logic

---

## 🔐 Admin Interface Discovery

### Telnet Interaction

```bash
telnet <target_ip> 8000
```

### Response

```
admin
Password:
```

➡️ Confirms:

* Service includes authentication layer
* Not HTTP — raw TCP protocol

---

## ⚡ Exploitation — Admin Access

* Authenticated into admin interface
* Service response:

```
Welcome Admin!!! Type "shell" to begin
```

---

## 👑 Privilege Escalation to Root

### Trigger Shell

```bash
shell
```

### Result

```bash
whoami
root
```

➡️ Full system compromise achieved

---

## 🏁 Root Access

```bash
cat /root/root.txt
```

(Flag redacted)

---

## 🧠 Key Vulnerabilities Identified

### 1. Arbitrary Code Execution

* Service evaluates user input directly

### 2. Insecure Git Repository

* Stored credentials in `.git/config`
* Exposed deleted sensitive files

### 3. Broken Authentication Logic

* Weak admin validation
* Accessible via raw TCP

### 4. Hidden Functionality Exposure

* `shell` command leads directly to root shell

---

## 🎯 Key Takeaways

* Always test services beyond HTTP (use netcat/telnet)
* Git repositories can leak:

  * Credentials
  * Source code
  * Deleted files
* Code review is powerful for exploitation
* Custom services often contain logic flaws
* Input validation is critical in secure coding

---



