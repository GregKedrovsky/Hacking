# Oopsie
> Remote: 10.129.199.96
> Remote: 10.129.185.6
> Remote: 10.129.95.191

> Local: 10.10.15.104

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




10.129.95.191



