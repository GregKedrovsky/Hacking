# Base

|     Local    |     Remote     |
| ------------ | -------------- |
| 10.10.15.155 | 10.129.95.184  |


----

## Nmap
```
# nmap 10.129.95.184  
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-24 09:58 EDT
Nmap scan report for 10.129.95.184
Host is up (0.045s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 1.08 seconds
```

```
# nmap -Pn -sS -sV -sC -O -p 22,80 10.129.95.184
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-24 09:59 EDT
Nmap scan report for 10.129.95.184
Host is up (0.043s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 f6:5c:9b:38:ec:a7:5c:79:1c:1f:18:1c:52:46:f7:0b (RSA)
|   256 65:0c:f7:db:42:03:46:07:f2:12:89:fe:11:20:2c:53 (ECDSA)
|_  256 b8:65:cd:3f:34:d8:02:6a:e3:18:23:3e:77:dd:87:40 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Welcome to Base
|_http-server-header: Apache/2.4.29 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.8 (96%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.04 seconds
```

## HTTP Enumeration

### OPTIONS
- Allowed: GET, POST, OPTIONS, HEAD
```
# curl -X OPTIONS 10.129.95.184 -v
*   Trying 10.129.95.184:80...
* Connected to 10.129.95.184 (10.129.95.184) port 80
> OPTIONS / HTTP/1.1
> Host: 10.129.95.184
> User-Agent: curl/8.8.0
> Accept: */*
> 
* Request completely sent off
< HTTP/1.1 200 OK
< Date: Sat, 24 Aug 2024 14:05:51 GMT
< Server: Apache/2.4.29 (Ubuntu)
< Allow: GET,POST,OPTIONS,HEAD
< Content-Length: 0
< Content-Type: text/html
< 
* Connection #0 to host 10.129.95.184 left intact
```

### NSE Banner
```
# nmap -Pn -p 80 --script banner 10.129.95.184
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-24 10:08 EDT
Nmap scan report for 10.129.95.184
Host is up (0.13s latency).

PORT   STATE SERVICE
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 11.59 seconds
```

