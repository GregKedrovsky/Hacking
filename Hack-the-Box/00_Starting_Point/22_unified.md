# Unified

|     Local    |     Remote     |
| ------------ | -------------- |
| 10.10.14.7   | 10.129.104.218 |
|    |  |

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
- Log4j: Java logging library.



