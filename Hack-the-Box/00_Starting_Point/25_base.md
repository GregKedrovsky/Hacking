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
```
# nmap -Pn -sV -p 80 --script http-methods 10.129.95.184 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-24 10:10 EDT
Nmap scan report for 10.129.95.184
Host is up (0.043s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.29 (Ubuntu)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.82 seconds
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

### robots.txt
- Nada
```
msf6 auxiliary(scanner/http/robots_txt) > run

[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

## Login
- Login page: `http://10.129.95.184/login/login.php`
- Login directory:

![image](https://github.com/user-attachments/assets/7441fb52-ce39-4ca2-97cc-2bfda219ab9f)

A swap (.swp) file store content for the specific file — for example, while you’re editing a file with vim. They are set up when you start an edit session and then automatically removed when you’re done unless some problem occurs and your editing session doesn’t complete properly. In that case, vim will offer you a chance to recover your work where you left off. [Source](https://www.networkworld.com/article/939724/what-are-unix-swap-swp-files.html).
- If the file being edited were called chkAccts.sh, for example, the swap file set up when you begin your edit would be called `.chkAccts.sh.swp`.
- You could spot it sitting in the same directory were you to look for it from another window or login session.

Therefore `login.php.swp` appears to be a swap file for a previous login that hung up.

I opened the .swp file in a text editor (there is a lot of garbage; it's not plain text). 
- **NOTE:** The login form uses the PHP function `strcmp()` ([string comparison](https://www.w3schools.com/php/func_string_strcmp.asp)) to compare the user entered username/password to saved values.
- The `strcmp()` function will return 0 (zero) if both values are the same (else it returns a positive or negative number).

## Directory Busting

### DirB
- DirB did not like large files of names. 
```
# dirb http://10.129.95.184                                                        

---- Scanning URL: http://10.129.95.184/ ----
==> DIRECTORY: http://10.129.95.184/assets/                                                                                                               
==> DIRECTORY: http://10.129.95.184/forms/                                                                                                                
+ http://10.129.95.184/index.html (CODE:200|SIZE:39344)                                                                                                   
==> DIRECTORY: http://10.129.95.184/login/                                                                                                                
+ http://10.129.95.184/server-status (CODE:403|SIZE:278)                                                                                                  
                                                                                                                                                          
---- Entering directory: http://10.129.95.184/assets/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                                                                                                          
---- Entering directory: http://10.129.95.184/forms/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                                                                                                          
---- Entering directory: http://10.129.95.184/login/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                               
-----------------
END_TIME: Sat Aug 24 10:53:28 2024
DOWNLOADED: 4612 - FOUND: 2

```

### DirBuster
- Found the same ones: assets, forms, logins. And then it found icons. Whoopteedoo...

### GoBuster

```
# gobuster dir -u http://10.129.95.184 -w /usr/share/wordlists/dirb/common.txt -b 403,404
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.95.184
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   403,404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/assets               (Status: 301) [Size: 315] [--> http://10.129.95.184/assets/]
/forms                (Status: 301) [Size: 314] [--> http://10.129.95.184/forms/]
/index.html           (Status: 200) [Size: 39344]
/login                (Status: 301) [Size: 314] [--> http://10.129.95.184/login/]
Progress: 4614 / 4615 (99.98%)
===============================================================
Finished
===============================================================
```

### Tweak my Directory File
- The HTB question shows the answer to have this syntax: `/_*****d`
- So, I took a couple of the common directory wordlist files and shoved them through this python script to prepend an underline to each value in the file:

```
import sys

if len(sys.argv) == 3: 

    # Open the file from the argument provided
    input_file = open(sys.argv[1], 'r')

    # Open the file to which to write:
    output_file = open(sys.argv[2], 'w')

    counter = 0

    # Append "_" to each line/value and write
    for line in input_file:
        print(line.rstrip("\n") + " >> " + "_" + line.rstrip("/n"), end='')
        output_file.write("_" + line)
        counter += 1

else:
    print("Usage: 'underline.py inputFileName outputFileName.")
    exit()

input_file.close()
output_file.close()

print()
print("Processed " + str(counter) + " directory names.")

exit()
```

Then I ran GoBuster with my modified file: 

```
# gobuster dir -u http://10.129.95.184 -w gman_common1.txt -b 403,404 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.95.184
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                gman_common1.txt
[+] Negative Status codes:   404,403
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/_uploaded            (Status: 301) [Size: 318] [--> http://10.129.95.184/_uploaded/]
Progress: 4614 / 4615 (99.98%)
===============================================================
Finished
===============================================================
```

**Answer:** `/_uploaded`

