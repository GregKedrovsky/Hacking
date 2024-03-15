# Pennyworth

> 10.129.59.175

## Nmap
```
nmap 10.129.59.175
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-14 21:37 EDT
Nmap scan report for 10.129.59.175
Host is up (0.049s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT     STATE SERVICE
8080/tcp open  http-proxy

Nmap done: 1 IP address (1 host up) scanned in 0.85 seconds
```

```
nmap -sS -sV -sC -O -p 8080 10.129.59.175
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-14 21:39 EDT
Nmap scan report for 10.129.59.175
Host is up (0.049s latency).

PORT     STATE SERVICE VERSION
8080/tcp open  http    Jetty 9.4.39.v20210325
|_http-title: Site doesn't have a title (text/html;charset=utf-8).
|_http-server-header: Jetty(9.4.39.v20210325)
| http-robots.txt: 1 disallowed entry 
|_/
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.8 (96%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.26 seconds
```

## Jetty
> Eclipse Jetty is a Java web server and Java Servlet container. While web servers are usually associated with serving documents to people, Jetty is now often used for machine to machine communications, usually within larger software frameworks.

## Jenkins
- Access the site: `http://10.129.59.175:8080`
- You get a login screen that says, "Welcome to Jenkins!" and asks for username and password.
- Found an nse script here: https://github.com/icarot/NSE_scripts/blob/master/jenkins-info.nse
  - Downloaded and just ran it...
```
nmap --script jenkins-info -p 8080 10.129.59.175
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-14 22:05 EDT
Nmap scan report for 10.129.59.175
Host is up (0.049s latency).

PORT     STATE SERVICE
8080/tcp open  http-proxy
| jenkins-info: 
|   Hudson Version: 1.395
|   Jenkins Version: 2.289.1
|   Jenkins Session ID: aa6c090a
|_  Server: Jetty(9.4.39.v20210325)

Nmap done: 1 IP address (1 host up) scanned in 0.44 seconds
```

Google search: "jenkins default credentials"
- Results: admin:password
- Didn't work. "admin" is a Windows login.
- **root:password** worked and I got in.



