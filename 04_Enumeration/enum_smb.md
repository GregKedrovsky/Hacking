# Enumeration: SMB
> SMB (Server Message Block) is a Windows implementation of a file sharing service.  The Linux version of SMB is called Samba

- Common ports on a Windows machine: 
  - TCP Port **135:** Microsoft EPMAP (End Point Mapper)
  - TCP Port **139:** NetBIOS Datagram Service
  - TCP Port **445:** Microsoft-DS Active Directory; SMB file sharing 
- If you need to list out the SMB shares in Windows: [net share](../net.md)
- [MetaSploit modules to use](https://www.offsec.com/metasploit-unleashed/port-scanning/#smb-version-scanning): `smb_version` | `smb_enumusers` | `smb_enumshares` | `smb_login` (then use [smbclient](#smbclient) outside of msf to logon)

## Contents
- [Windows Net Use](#windows-net-use)
- [Nmap Scripts](#nmap-scripts)
  - [smb-protocols](#smb-protocols)
  - [smb-security-mode](#smb-security-mode)
  - [smb-enum-sessions](#smb-enum-sessions)
  - [smb-enum-shares](#smb-enum-shares)
  - [smb-enum-users](#smb-enum-users)
  - [smb-server-stats](#smb-server-stats)
  - [smb-enum-domains](#smb-enum-domains)
  - [smb-enum-groups](#smb-enum-groups)
  - [smb-enum-services](#smb-enum-services)
- [SMBMap](#smbmap)
  - [Null Session](#null-session)
  - [Login with Creds](#login-with-creds)
  - [Run a Command](#run-a-command)
  - [List Drives on the Share](#list-drives-on-the-share)
  - [Connect to a Drive](#connect-to-a-drive)
  - [Upload a File](#upload-a-file)
  - [Download a File](#download-a-file)
- [nmblookup](#nmblookup)
- [smbclient](#smbclient)
  - [General Syntax](#general-syntax)
  - [Connect](#connect)
- [rpcclient](#rpcclient)
  - [General Syntax](#general-syntax-1)
- [enum4linux](#enum4linux)
  - [Get O/S Info](#get-os-info)
  - [Get Share Info](#get-share-info)
  - [Get Groups & Members](#get-groups--members)
  - [Get (Printer) Information:](#get-printer-information)
  - [Get SIDs (Linux)](#get-sids-linux)
- [Dictionary Attacks](#dictionary-attacks)
  - [MetaSploit](#metasploit)
  - [Hydra](#hydra)

## Windows Net Use
> Windows specific commands (net).
```
net use /?                                               # help
net use * /delete                                        # clear all stored sessions: 
net use /delete \\10.3.31.125\C$                         # clear a specific stored session
net use z: \\10.3.31.125\C$                              # mount a target on a specific drive letter
net use * \\10.3.31.125\C$                               # mount a target on whatever letter
net use g: \\10.3.31.125\C$ [password] /user:[username]  # mount the share
```

## Nmap Scripts
> If after your initial scan you know an SMB server exists on a machine, you need to enumerate it (to find something to exploit). And you can do that with nmap scripts.
- [Nmap Scripts](https://nmap.org/nsedoc/scripts): `ctrl-f` on that page and search for "smb-" to see the SMB scripts

### smb-protocols
> List the supported protocols and dialects of an SMB server.
```
nmap -p445 --script smb-protocols 10.0.17.200
```

### [smb-security-mode](https://nmap.org/nsedoc/scripts/smb-security-mode.html)
> Information about the SMB security level.
```
nmap -p445 --script smb-security-mode 10.0.17.200
```

### smb-enum-sessions
> Enumerating the users logged into a system through an SMB share. 
- First, we won’t use any credentials to see the output.
```
nmap -p445 --script smb-enum-sessions 10.0.17.200
```

- If the guest login is not enabled we can always use valid credentials of the target machine to discover the same information.
```
nmap -p445 --script smb-enum-sessions --script-args smbusername=[username],smbpassword=[password] 10.0.17.200
```

### smb-enum-shares
> Scan target for shares. 
- If guest login allowed, you should get a list: 
```
nmap -p445 --script smb-enum-shares 10.0.17.200
```

- Scan all shares using valid credentials to check the permissions: 
```
nmap -p445 --script smb-enum-shares --script-args smbusername=[username],smbpassword=[password] 10.0.17.200
```

- Enumerate all the shared folders and drives then run the `ls` command on all the shared folders.
```
nmap -p445 --script smb-enum-shares,smb-ls --script-args smbusername=[username],smbpassword=[password] 10.0.17.200
```

### smb-enum-users
> Enumerate the windows users on a target machine.
```
nmap -p445 --script smb-enum-users --script-args smbusername=[username],smbpassword=[password] 10.0.17.200
```

### smb-server-stats
> Get information about the server statistics. It uses port 445 and port 139 to fetch the details.
```
nmap -p445 --script smb-server-stats --script-args smbusername=[username],smbpassword=[password] 10.0.17.200
```

### smb-enum-domains
> Enumerating available domains on a target machine.
```
nmap -p445 --script smb-enum-domains --script-args smbusername=[username],smbpassword=[password] 10.0.17.200
```

### smb-enum-groups
> Enumerating available user groups on a target machine.
```
nmap -p445 --script smb-enum-groups --script-args smbusername=[username],smbpassword=[password] 10.0.17.200
```

### smb-enum-services
> Enumerating services on a target machine.
```
nmap -p445 --script smb-enum-services --script-args smbusername=[username],smbpassword=[password] 10.0.17.200
```

## SMBMap
> Samba Share Enumerator
- SMBMap allows users to enumerate samba share drives across an entire domain.
- List share drives, drive permissions, share contents, upload/download functionality, file name auto-download pattern matching, and even execute remote commands.
- This tool was designed with pen testing in mind, and is intended to simplify searching for potentially sensitive data across large networks.

### Null Session
```
smbmap -u guest -p "" -d . -H 10.1.1.1
```
- Login as guest w/o a password
- Option `-d` with arg `.` is the current directory (remember `.` and `..` in Linux: current directory and one up, respectively).
- If your target is configured well, you'll see the shares but see you have no access to anything but things like IPC$ and PRINTERS$, and then you only have read access.

### Login with Creds
```
smbmap -u [username] -p [password] -d . -H 10.1.1.1
```
- If the creds are good, you get in and get more access.

### Run a Command
```
smbmap -u [username] -p [password] -H 10.1.1.1 -x 'ipconfig'
```
- If that works, you know you have the ability to execute code remotely

### List Drives on the Share
```
smbmap -u [username] -p [password] -H 10.1.1.1 -L
```

### Connect to a Drive
```
smbmap -u [username] -p [password] -H 10.1.1.1 -r 'C$'
```
- This is like running a `dir` (Windows) or `ls` (Linux).

### Upload a File
```
smbmap -u [username] -p [password] -H 10.1.1.1 --upload '/root/test.txt' 'C$\test.txt'
```
- Uploads the file to the C: drive on the target.
- Run the `-r` command again to check and make sure it uploaded.

### Download a File
```
smbmap -u [username] -p [password] -H 10.1.1.1 --download 'C$\flag.txt'
```

## nmblookup
> NetBIOS over TCP/IP client used to lookup NetBIOS names. Used to query NetBIOS names and map them to IP addresses in a network using NetBIOS over TCP/IP queries.
```
nmblookup -A [target IP addr]
```
- The `-A` option: lookup by IP Address ( A = Address )
- The `<20>` in the result indicates a server we can actually connect

## smbclient
> ftp-like client to access SMB/CIFS resources on servers. It's a client that can 'talk' to an SMB/CIFS server. It offers an interface similar to that of the ftp program. Operations include things like getting files from the server to the local machine, putting files from the local machine to the server, retrieving directory information from the server and so on.

### General Syntax
```
smbclient -L [target ip addr] -N
```
- The `-L` lists the shares available. Gives you server info.
- The `-N` checks for a null session (i.e., no pwd required).
  - OJO, Syntax: `smbclient -L -N [target ip addr]` does NOT work.
  - The `-N` goes **_AFTER_** the target IP address.
- Then hit ENTER when it asks for the password (i.e., enter null value)
- NOTE: If you see an IPC$ share with a null session, you might be able to connect to it.

### Connect
```
smbclient //[target IP]/Public -N
```
- Connect to the target SMB server, the public share, with no password
- `> help`  (see commands you can run)
- You drop into subdirs... find the flag, etc.
- `> get flag.txt` (should download it)

## rpcclient
>Tool for executing client side MS-RPC functions

### General Syntax
```
rpcclient -U "" -N [target ip addr]
```
- This tries to connect with (-U) a null username (none) and (-N) no password.
- If you connect, type ? to see a help screen.

## [enum4linux](https://labs.portcullis.co.uk/tools/enum4linux/)
> Enum4linux is a tool for enumerating information from Windows and Samba systems. It is written in PERL and is basically a wrapper around the Samba tools smbclient, rpclient, net and nmblookup. 

### Get O/S Info
```
enum4linux -o [target IP addr]
```

### Get Share Info
```
enum4linux -S [target IP]
```
- Similar to: `nmap -p 445 --script smb-enum-shares [target IP]`
- Similar to: `msfconsole >> use auxiliary/scanner/smb/smb_enumshare`

### Get Groups & Members
```
enum4linux -G [target IP]
```

### Get (Printer) Information:
```
enum4linux -i [target IP]
```

### Get SIDs (Linux)
```
enum4linux -r -u "admin" -p [password] [target IP]
```
- The -`r` switch should enumerate users via RID cycling

## Dictionary Attacks

### MetaSploit
> msfconsole: Enumerate the target SMB share using a wordlist...
```
> use auxiliary/scanner/smb/smb_login
> info  # this gives you a Description of the smb_login module
> set rhosts [target IP]
> set pass_file /usr/share/wordlists/metasploit/unix_passwords.txt
> set smbuser [username]
> options
> run 
```  

### [Hydra](hydra.md)
> Hydra is a tool to guess/crack valid login/password pairs.
- First you need may need to unzip the RockYou wordlist:
```
gzip -d /usr/share/wordlists/rockyou.txt.gz .
```
- Then we can use that resulting file with Hydra...
```
hydra -l admin -P /usr/share/wordlists/rockyou.txt [target IP] smb
```
- The `-l` is for Login (login as admin)
  - Note: lower case letters for options are for specific values
- The `-P` is for a password file 
  - Note: upper case letters for options are for files (lists)
- The `smb` after the target IP the protocol we're attacking

