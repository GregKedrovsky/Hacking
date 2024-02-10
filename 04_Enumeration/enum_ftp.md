# Enumeration: FTP
> File Transfer Protocoal (FTP) runs default on port 21 is used for storing files on a server and being able to access them remotely.

## Anonymous Login
```
ftp [ip]
```
- ENTER for username and ENTER for password (see if you get in).

## [Hydra](../hydra.md)
```
hydra -L [path/to/username_file.txt] -P [path/to/password_file.txt] [target ip] ftp
```
- `L` is for logins and `-P` is for passwords (uppercase for file, lowercase for one value)
- Tries every password for every username in the files.
