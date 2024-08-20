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
## Port 80

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

Ran a `searchsploit Apache 2.4.29`: one priv esc that might be useful

## Scan UDP Ports (slow...)

```
# nmap -Pn -sU 10.129.95.185
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-20 18:54 EDT
Nmap scan report for 10.129.95.185
Host is up (0.28s latency).
Not shown: 980 closed udp ports (port-unreach)
PORT      STATE         SERVICE
68/udp    open|filtered dhcpc
69/udp    open|filtered tftp
772/udp   open|filtered cycleserv2
773/udp   open|filtered notify
789/udp   open|filtered unknown
1059/udp  open|filtered nimreg
1072/udp  open|filtered cardax
1214/udp  open|filtered fasttrack
3457/udp  open|filtered vat-control
17077/udp open|filtered unknown
17468/udp open|filtered unknown
19322/udp open|filtered unknown
20019/udp open|filtered unknown
20717/udp open|filtered unknown
21111/udp open|filtered unknown
28122/udp open|filtered unknown
44923/udp open|filtered unknown
49170/udp open|filtered unknown
49182/udp open|filtered unknown
62677/udp open|filtered unknown

Nmap done: 1 IP address (1 host up) scanned in 1314.76 seconds

```
