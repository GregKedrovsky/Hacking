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

Loaded the page in a browser (not much there). 

**Walk-Through:** ...if we take a look at the URL we can see that this has automatically changed to `http://{target_IP}/?file=home.php`. This is a common way that developers use to dynamically load pages in a website and if not programmed correctly it can often lead to the webpage being vulnerable to Local File Inclusion...

**[Local File Inclusion[../../local_file_inclusion.md)**: The File Inclusion vulnerability allows an attacker to include a file, usually exploiting a “dynamic file inclusion” mechanisms implemented in the target application. The vulnerability occurs due to the use of user-supplied input without proper validation.

This can lead to something as outputting the contents of the file, but depending on the severity, it can also lead to:
- Code execution on the web server
- Code execution on the client-side such as JavaScript which can lead to other attacks such as cross site scripting (XSS)
- Denial of Service (DoS)
- Sensitive Information Disclosure

LFI is the process of including files, ***that are already locally present on the server***, through the exploiting of vulnerable inclusion procedures implemented in the application.

Easy test for LFI: `http://10.129.95.185/?file=../../../../../etc/passwd` and see if you get the file contents. If you do, it's vulnerable.

### DirBusting with dirb

```
# dirb http://10.129.95.185

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Tue Aug 20 19:12:13 2024
URL_BASE: http://10.129.95.185/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://10.129.95.185/ ----
==> DIRECTORY: http://10.129.95.185/fonts/                                                                                                                 
==> DIRECTORY: http://10.129.95.185/images/                                                                                                                
+ http://10.129.95.185/index.php (CODE:301|SIZE:308)                                                                                                       
+ http://10.129.95.185/server-status (CODE:403|SIZE:278)                                                                                                   
                                                                                                                                                           
---- Entering directory: http://10.129.95.185/fonts/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                                                                                                           
---- Entering directory: http://10.129.95.185/images/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                               
-----------------
END_TIME: Tue Aug 20 19:16:04 2024
DOWNLOADED: 4612 - FOUND: 2
```


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

## tftp (UDP port 69)
> Trivial File Transfer Protocol (TFTP) is a simple protocol that provides basic file transfer function with no user authentication. 

Need some version information: (see man page)
```
# tftp -V 10.129.95.185         
tftp-hpa 5.3, without readline
```

