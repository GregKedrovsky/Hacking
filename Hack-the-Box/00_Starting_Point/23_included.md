# Included

|     Local    |     Remote     |
| ------------ | -------------- |
| 10.10.15.155 | 10.129.95.185  |

----

## Nmap

```
# nmap -Pn 10.129.95.185
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-20 18:51 EDT
Nmap scan report for 10.129.95.185
Host is up (0.044s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 0.86 seconds
```

```
# nmap -Pn -p 1-10000 10.129.95.185
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-20 18:52 EDT
Nmap scan report for 10.129.95.185
Host is up (0.045s latency).
Not shown: 9999 closed tcp ports (reset)
PORT   STATE SERVICE
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 4.51 seconds
```

```
# nmap -Pn -sV -sC -p 80 10.129.95.185
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-20 19:01 EDT
Nmap scan report for 10.129.95.185
Host is up (1.00s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
| http-title: Site doesn't have a title (text/html; charset=UTF-8).
|_Requested resource was http://10.129.95.185/?file=home.php

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.66 seconds
```

```
nmap -Pn -sU 10.129.95.185
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-20 18:54 EDT


```
