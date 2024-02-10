# Enumeration: FTP
> File Transfer Protocoal (FTP) runs default on port 21 is used for storing files on a server and being able to access them remotely.

## Contents
- [Anonymous Login](#anonymous-login)
  - [Nmap](#nmap)
  - [Attempt Login](#attempt-login)
- [Hydra](#hydra)

## Anonymous Login

### Nmap
```
nmap [ip] -p 21 --script ftp-anon
```
- Results: it shows you CAN access with anonymous login
- Also: check the perms; you may get read access and some write and execute
- 
### Attempt Login
```
ftp [ip]
```
- Username: anonymous
- Passwords: just hit ENTER for null
- You'll get a "Login successful" if it works

## [Hydra](../hydra.md)
```
hydra -L [path/to/username_file.txt] -P [path/to/password_file.txt] [target ip] ftp
```
- `L` is for logins and `-P` is for passwords (uppercase for file, lowercase for one value)
- Tries every password for every username in the files.
