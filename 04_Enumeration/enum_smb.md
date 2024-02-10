# Enumeration: SMB
> SMB (Server Message Block) is a Windows implementation of a file sharing service.  The Linux version of SMB is called Samba

## Contents
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
- [Hydra](#hydra)

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
smbmap -u administrator -p [password] -d . -H 10.1.1.1
```
- If the creds are good, you get in and get more access.

### Run a Command
```
smbmap -u administrator -p [password] -H 10.1.1.1 -x 'ipconfig'
```
- If that works, you know you have the ability to execute code remotely

### List Drives on the Share
```
smbmap -u administrator -p [password] -H 10.1.1.1 -L
```

### Connect to a Drive
```
smbmap -u administrator -p [password] -H 10.1.1.1 -r 'C$'
```
- This is like running a `dir` (Windows) or `ls` (Linux).

### Upload a File
```
smbmap -u administrator -p [password] -H 10.1.1.1 --upload '/root/test.txt' 'C$\test.txt'
```
- Uploads the file to the C: drive on the target.
- Run the `-r` command again to check and make sure it uploaded.

### Download a File
```
smbmap -u administrator -p [password] -H 10.1.1.1 --download 'C$\flag.txt'
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

## Hydra

Dictionary Attacks. [See here](hydra.md).
