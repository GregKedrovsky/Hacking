# Enumeration: SSH
> Secure Shell (SSH): Used for remote administration (gives you a "secure shell"). Allows you to interact with the remove machine through that shell. Port 22 is the default port for SSH.

## Contents
- [Initial Recon](#initial-recon)
- [General Syntax](#general-syntax)
- [Netcat](#netcat)
- [Nmap Enum](#nmap-enum)
- [RSA Key](#rsa-key)
- [Weak Passwords](#weak-passwords)
- [Dictionary Attacks](#dictionary-attacks)
- [Hydra](#hydra)
- [Nmap](#nmap)
- [MetaSploit](#metasploit)

## Initial Recon

### General Syntax
```
ssh [username]@[target IP]
```
- Attempt a root login: `ssh root@192.168.1.1`
- You might get info in a banner even before successful login

### Netcat
```
nc -nv [ip] [port]
```
- You can use `nc` to connect in much the same way as with `ssh`
  - `-n` : no DNS lookup
  - `-v` : verbose
- This might give you a banner that could let you know about the target SSH server.

### Nmap Enum
```
nmap [ip] -p 22 --script ssh2-enum-algos
```
- This will enumerate all the algorithms
- This will show us all the algorithms that can be used to create the key (RSA key).
- Write them down for evaluation and possible use later

### RSA Key
```
nmap [ip] -p 22 --script ssh-hostkey --script-args ssh_hostkey=full
```
- This may give you the full SSH-RSA host key.
- DEFINITELY want to save this if you get it

### Weak Passwords
```
nmap [ip] -p 22 --script ssh-auth-methods --script-args="ssh.user=student"
nmap [ip] -p 22 --script ssh-auth-methods --script-args="ssh.user=admin"
```
- Write down results (e.g., "public key" and "password")
- There could be usernames configured with no password

## Dictionary Attacks

### [Hydra](../hydra.md)
> Use Hydra to try to brute-force SSH login creds.
```
hydra -l [username] -P rockyou.txt [ip] ssh
```

### Nmap
- First create a users.txt file with several common usernames (administrator, admin, root, etc.; or use a prefabbed list).
- This script has its own wordlist for passwords.
```
nmap [ip] -p 22 --script ssh-brute --script-args userdb=/root/users.txt
```

### MetaSploit
- In msfconsole:
```
> use auxiliary/scanner/ssh/ssh_login
> tweak your options (rhosts, username, password, etc.)
> set userpass_file /usr/share/wordlist/metasploit/root_userpass.txt
> set STOP_ON_SUCCESS true
> set verbose true
> run
```




