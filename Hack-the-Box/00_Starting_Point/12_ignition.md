# Ignition
----

TARGET MACHINE IP ADDRESS
10.129.1.27
10.129.14.57  (respawn)
10.129.1.27  (third time's a charm...)

----
## KEY: /etc/hosts
- This deals with name-based VHosting (or Virtual Hosting). 
- The site `ignition.htb` is hosted on the same server (IP: `10.129.14.57`) as other websites. They share the server and the IP address.
- The problem we run into on this machine is that the server is not configured correctly: it does not redirect us from the IP we entered to the domain name we want (ignition.htb).
- And we can't just plub in `http://ignition.htb` because it's not in the DNS system for resolution.
- **Solution:** Add `10.129.14.57    ignition.htb`  to your `/etc/hosts` file so your browser can resolve the IP to the domain name.

----
# Nmap

```
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-21 17:27 EST
Nmap scan report for 10.129.1.27
Host is up (0.049s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 0.87 seconds
```

```
nmap -Pn -p 1-10000 10.129.1.27
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-21 17:29 EST
Nmap scan report for 10.129.1.27
Host is up (0.070s latency).
Not shown: 9999 closed tcp ports (reset)
PORT   STATE SERVICE
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 7.70 seconds
```

```
nmap -Pn -sS -sV -sC -O -T4 -p 80 10.129.1.27
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-21 17:30 EST
Nmap scan report for 10.129.1.27
Host is up (0.044s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    nginx 1.14.2
|_http-server-header: nginx/1.14.2
|_http-title: Did not follow redirect to http://ignition.htb/
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.8 (96%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.01 seconds
```

## Browser
- Cannot get on the page. Doesn't load (like above in nmap: redirect invalid)
## Dir Busting
- Same results with `dirb`

```
gobuster dir -u 10.129.1.27 -w /usr/share/wordlists/dirb/common.txt -r
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.1.27
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Follow Redirect:         true
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================

Error: error on running gobuster: unable to connect to http://10.129.1.27/: Get "http://ignition.htb/": dial tcp: lookup ignition.htb on 1.1.1.1:53: no such host

```
## curl
- `302`: the requested resource has been temporarily moved to a new location

```
curl http://10.129.1.27 -v
*   Trying 10.129.1.27:80...
* Connected to 10.129.1.27 (10.129.1.27) port 80
> GET / HTTP/1.1
> Host: 10.129.1.27
> User-Agent: curl/8.5.0
> Accept: */*
> 
< HTTP/1.1 302 Found
< Server: nginx/1.14.2
< Date: Wed, 21 Feb 2024 22:38:02 GMT
< Content-Type: text/html; charset=UTF-8
< Transfer-Encoding: chunked
< Connection: keep-alive
< Set-Cookie: PHPSESSID=i3enrk334mhrf2b6u5r8js3m67; expires=Wed, 21-Feb-2024 23:38:02 GMT; Max-Age=3600; path=/; domain=10.129.1.27; HttpOnly; SameSite=Lax
< Location: http://ignition.htb/
< Pragma: no-cache
< Cache-Control: max-age=0, must-revalidate, no-cache, no-store
< Expires: Tue, 21 Feb 2023 22:38:02 GMT
< Content-Security-Policy-Report-Only: font-src data: 'self' 'unsafe-inline'; form-action secure.authorize.net test.authorize.net geostag.cardinalcommerce.com geo.cardinalcommerce.com 1eafstag.cardinalcommerce.com 1eaf.cardinalcommerce.com centinelapistag.cardinalcommerce.com centinelapi.cardinalcommerce.com 'self' 'unsafe-inline'; frame-ancestors 'self' 'unsafe-inline'; frame-src fast.amc.demdex.net secure.authorize.net test.authorize.net geostag.cardinalcommerce.com geo.cardinalcommerce.com 1eafstag.cardinalcommerce.com 1eaf.cardinalcommerce.com centinelapistag.cardinalcommerce.com centinelapi.cardinalcommerce.com www.paypal.com www.sandbox.paypal.com player.vimeo.com *.youtube.com 'self' 'unsafe-inline'; img-src assets.adobedtm.com amcglobal.sc.omtrdc.net dpm.demdex.net cm.everesttech.net widgets.magentocommerce.com data: www.googleadservices.com www.google-analytics.com www.paypalobjects.com t.paypal.com www.paypal.com fpdbs.paypal.com fpdbs.sandbox.paypal.com *.vimeocdn.com i.ytimg.com s.ytimg.com data: 'self' 'unsafe-inline'; script-src assets.adobedtm.com secure.authorize.net test.authorize.net www.googleadservices.com www.google-analytics.com www.paypalobjects.com js.braintreegateway.com www.paypal.com geostag.cardinalcommerce.com 1eafstag.cardinalcommerce.com geoapi.cardinalcommerce.com 1eafapi.cardinalcommerce.com songbird.cardinalcommerce.com includestest.ccdc02.com www.sandbox.paypal.com t.paypal.com s.ytimg.com www.googleapis.com vimeo.com www.vimeo.com *.vimeocdn.com www.youtube.com video.google.com 'self' 'unsafe-inline' 'unsafe-eval'; style-src getfirebug.com 'self' 'unsafe-inline'; object-src 'self' 'unsafe-inline'; media-src 'self' 'unsafe-inline'; manifest-src 'self' 'unsafe-inline'; connect-src dpm.demdex.net amcglobal.sc.omtrdc.net www.google-analytics.com geostag.cardinalcommerce.com geo.cardinalcommerce.com 1eafstag.cardinalcommerce.com 1eaf.cardinalcommerce.com centinelapistag.cardinalcommerce.com centinelapi.cardinalcommerce.com 'self' 'unsafe-inline'; child-src http: https: blob: 'self' 'unsafe-inline'; default-src 'self' 'unsafe-inline' 'unsafe-eval'; base-uri 'self' 'unsafe-inline';
< X-Content-Type-Options: nosniff
< X-XSS-Protection: 1; mode=block
< X-Frame-Options: SAMEORIGIN
< 
* Connection #0 to host 10.129.1.27 left intact
```

## DirBuster

```
dirbuster
# Results: 
# http://10.129.1.27:80/index.php
# http://10.129.1.27:80/setup/
# http://10.129.1.27:80/soap/
# http://10.129.1.27:80/setup/index.php
# http://10.129.1.27:80/setup/index.php/
```

## GoBuster on SubDir
- Got nothing on the /soap/subdir

```
gobuster dir -u http://10.129.1.27/setup -w /usr/share/wordlists/dirb/common.txt -x php
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.1.27/setup
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              php
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/index.php            (Status: 200) [Size: 2827]
/index.php            (Status: 200) [Size: 2827]
Progress: 9228 / 9230 (99.98%)
===============================================================
Finished
===============================================================

```

----
#### Respawned. New IP: `10.129.14.57`

---

## Walk-Through
- Had to read through the walk-through to get this one... Totally lost. And, the first step is not replicable (i.e., the Google search on the browser error). 
- The idea here is that our target is part of virtual hosting (several domains sharing the same IP address). 
- The webserver we are making requests to is throwing us an error because we haven't specified a certain hostname out of the ones that could be hosted on that same target IP address. 
- From here, we'd think that simply inputting ignition.htb instead of the target IP address into the search bar would solve our issue, but unfortunately, this is not the case. When entering a hostname instead of an IP address as the request's destination, there is a middleman involved that you might not know about: DNS (and this domain is not in DNS).
- `curl -v http://10.129.14.57` 
	- The 302 Found response, together with the Location header, indicates that the resource we requested ( / ) has been (temporarily) moved to http://ignition.htb/ . This means that our assumptions were true.
- To solve the issue we are currently facing here, we will modify our local DNS file named hosts located in the /etc directory. 

## Modified /etc/hosts 
- Add a line to resolve the target IP to `ignition.htb`

```
vim /etc/hosts 

cat /etc/hosts
127.0.0.1       localhost
127.0.1.1       kali
::1             localhost ip6-localhost ip6-loopback
ff02::1         ip6-allnodes
ff02::2         ip6-allrouters
10.129.14.57    ignition.htb                             ## Added this line.
```

## Browser
- No either will work in the URL:  `10.129.14.57` or `ignition.htb`

## GoBuster
```
gobuster dir -u http://10.129.1.27 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -r
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.1.27
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Follow Redirect:         true
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/contact              (Status: 200) [Size: 28673]
/home                 (Status: 200) [Size: 25802]
/0                    (Status: 200) [Size: 25803]
/catalog              (Status: 200) [Size: 25806]
/admin                (Status: 200) [Size: 7092]
/Home                 (Status: 200) [Size: 25802]
/cms                  (Status: 200) [Size: 25817]
Progress: 1012 / 87665 (1.15%)[ERROR] context deadline exceeded (Client.Timeout or context cancellation while reading body)
/checkout             (Status: 200) [Size: 26102]
/robots               (Status: 200) [Size: 1]

... etc. (it was going on for a very, very, very long time...)

```

## SubDir: admin
- That subdir looks interesting, so I looked in it via my browser.
- `http://ignition.htb/admin`

![Pasted image 20240222184908](https://github.com/GregKedrovsky/Hacking/assets/26492233/def4cbfb-8fb0-4315-acce-696049522204)

## Password
- The question for the flag has two parts: 
	- Look up the password requirements for Magento and
	- Try searching for the most common passwords of 2023. 
- Which password provides access to the admin account? 
- I have no idea what they're looking for with regard to password requirements.
- But I did find a nice, fine list of the most common passwords of 2023 (OJO: if you just copy and paste from a website, **_watch your whitespace_**): 
```
123456
password
123456789
12345
12345678
qwerty
1234567
111111
1234567890
123123
abc123
1234
password1
iloveyou
1q2w3e4r
000000
qwerty123
zaq12wsx
dragon
sunshine
princess
letmein
654321
monkey
27653
1qaz2wsx
123321
qwertyuiop
superman
asdfghjkl
```

### Burp Suite
- Installed FoxyProxy in my browser and fired up Burp Suite to do some sniping.
- Proxy Tab... "Intercept is on"... refreshed Magento login screen and captured the result.
	- NOT what I wanted. 
	- I wanted the "Sign In" button so I can grab the username:password combo
- Same thing, captured the comb admin:x
- Send to Intruder
- Intruder: clear all section markers and place section markers ONLY around the password.
	- Because we know the username we want to use: admin
	- We want each of our 30 passwords tried in that placeholder.
- Pasted in my list in "Payload settings" of the Payloads subtab. "Start attack"

## Pwn
- Password for admin: `qwerty123`
	- But that didn't work in the flag box...
	- But it got me into the Magento dashboard where the flag was.
- your flag is: 797d6c988d9dc5865e010b9410f247e0

![Pasted image 20240222192536](https://github.com/GregKedrovsky/Hacking/assets/26492233/4e82acb4-a5f6-4ce5-a24c-0c231d4f433b)
