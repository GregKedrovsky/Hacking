# Enumeration: SMB
> SMB (Server Message Block) is a Windows implementation of a file sharing service.  The Linux version of SMB is called Samba

## Contents
- [](*)
- [](*)
- [](*)
- [](*)
- [](*)

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

### Create & Upload a File
```
smbmap -u administrator -p [password] -H 10.1.1.1 --upload '/root/test.txt' 'C$\test.txt'
```
- Uploads the file to the C: drive on the target.
- Run the `-r` command again to check and make sure it uploaded.

## nmblookup


## smbclient


## rpcclient

## enum4linux

## Hydra

Dictionary Attacks. [See here](hydra.md).




























