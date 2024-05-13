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
