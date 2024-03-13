# Funnel

> 10.129.228.195

## Nmap
```
nmap 10.129.228.195
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-13 19:33 EDT
Nmap scan report for 10.129.228.195
Host is up (0.039s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
```
```
nmap -sS -sV -sC -O -p 21,22 -T4 10.129.228.195
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-13 19:35 EDT
Nmap scan report for 10.129.228.195
Host is up (0.043s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.8 (96%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 - 5.4 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.02 seconds
```

## FTP Enumeration
```
nmap --script ftp-anon -p 21 10.129.228.195
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-13 19:37 EDT
Nmap scan report for 10.129.228.195
Host is up (0.040s latency).

PORT   STATE SERVICE
21/tcp open  ftp
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 ftp      ftp          4096 Nov 28  2022 mail_backup

Nmap done: 1 IP address (1 host up) scanned in 0.60 seconds
```
## FTP Login
1. Logged in as `anonymous` (no password/null password)
2. Did a `pwd` and was in a root directory for ftp (not the actual root)
3. Did an `ls` and saw there was one subdir: `mail_backup`
4. `cd` into the subdir and an `ls` revealed two files that I downloaded:

```
ftp> ls
229 Entering Extended Passive Mode (|||6644|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp         58899 Nov 28  2022 password_policy.pdf
-rw-r--r--    1 ftp      ftp           713 Nov 28  2022 welcome_28112022
226 Directory send OK.

ftp> get password_policy.pdf
local: password_policy.pdf remote: password_policy.pdf
229 Entering Extended Passive Mode (|||38072|)
150 Opening BINARY mode data connection for password_policy.pdf (58899 bytes).
100% |**************************************************************************************************************| 58899      695.91 KiB/s    00:00 ETA
226 Transfer complete.
58899 bytes received in 00:00 (474.45 KiB/s)

ftp> get welcome_28112022
local: welcome_28112022 remote: welcome_28112022
229 Entering Extended Passive Mode (|||56481|)
150 Opening BINARY mode data connection for welcome_28112022 (713 bytes).
100% |**************************************************************************************************************|   713       13.87 MiB/s    00:00 ETA
226 Transfer complete.
713 bytes received in 00:00 (34.43 KiB/s)
```

## password_policy.pdf
- Doc says: "...the default password of “funnel123#!#” must be changed immediately."
- The `welcome` file is the same as the `password` file.
