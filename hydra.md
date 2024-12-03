# [Hydra](https://rodtrent.substack.com/p/using-kali-linux-and-hydra-for-attack)
> Hydra is extremely functional for brute-forcing MULTIPLE different protocols.

You may need to unzip the RockYou wordlist:
```
gzip -d /usr/share/wordlists/rockyou.txt.gz .
```

If Hydra throws an error or for some reason does not work, try [CrackMapExec](cme.md).

## Contents
- [Syntax](#general-syntax-by-usage-example)
- [Configuration](#configuration)
  - [Speed Kills, Del](#speed-kills-del)
  - [Common Options](#common-options)
- [Protocol Examples](#specific-protocol-examples): [`FTP`](#ftp), [`SMB`](#smb), [`SSH`](#ssh), [`MySQL`](#mysql), [`WebDAV`](#webdav), [`RDP`](#rdp)
- [HTTP Login Form: http-post-form](#http-login-form-http-post-form)
  - [General Syntax](#general-syntax)
  - [Intercept a Random, Invalid Login](#intercept-a-random-invalid-login)
  - [Capture a Failed Login String](#capture-a-failed-login-string)
  - [Final Syntax](#final-syntax)
  - [Additional Help](#additional-help)
- [Hydra SSH Error]()

## General Syntax (by usage example):

```
hydra -l root -P /usr/share/wordlists/metasploit/unix_passwords.txt ssh://192.168.1.123:22 -t 6 -V
```

- `-l root` : attempt to login as the root user
- `-P /usr/.../unix_passwords.txt` : using a password list
- `ssh://192.168.1.123` : attacking SSH at the target IP
- `:22` : via port 22 on the target
- `-t 6` : with 6 threads
- `V` : with verbosity to see the attempts 

## Configuration

### Common Options
- `-L` or `-l` : Indicates the "Login" (capital: file of multiple; lower case for single)
- `-P` or `-p`: Indicates the "Passwords" (capital: file of multiple; lower case for single)

### Speed Kills, Del
- Be very careful with your brute-force speed. You could crash the system or DoS it. 
- The `-t` (tasks) option sets the number of connects in parallel (default is 16)
- You could set `-t 2` or `-t 3` and see if that plays nicer. Hydra actually recommends `-t 1` through `-t 4`.

## Specific Protocol Examples

### FTP
```
hydra -L [path/to/username_file.txt] -P [path/to/password_file.txt] [target ip] ftp
```

### SMB
```
hydra -l admin -P /usr/share/wordlists/rockyou.txt [target IP] smb
```

### SSH
```
hydra -l student -P rockyou.txt [ip] ssh
# or
hydra -l student -P rockyou.txt ssh://[ip]
```

### MySQL
```
hydra -l root -P /usr/share/metasploit_framework/data/wordlists/unix_passwords.txt [target ip] mysql
```

### WebDAV
```
hydra -L /.../common-users.txt -P  /.../common-passwords.txt [target ip] http-get /webdav/
```
- `http-get` : Indicates the protocol to be used
- `/webdav/` : Indicates the directory where hydra can find the authentication mechanism

### RDP
```
hydra -L users.txt -P passwords.txt [target ip] rdp -s 3333
```
- `-s` : service port (if different than the default)

## HTTP Login Form: http-post-form
> This was taken from a lab (and the syntax is complicated). The lab used bWAPP as the vulnerable target website and Burp Suite as the proxy.

### General Syntax
```
<url>:<form parameters>:<condition string>[:<optional>[:<optional>]
#[1]    [2]             [3]  
```
1. First is the page on the server to GET from or POST to (URL of the login page)
2. Second is the POST/GET variables (taken from either the browser, proxy, etc. with url-encoded (resp. base64-encoded) usernames and passwords being replaced in the "^USER^" (resp. "^USER64^") and "^PASS^" (resp. "^PASS64^") placeholders (FORM PARAMETERS).
3. Third is the string that it checks for an *invalid* login (by default)
    - This is where most people get it wrong. You have to check the webapp what a failed string looks like and put it in this parameter!

### Intercept a Random, Invalid Login
- Copy and paste the login syntax being POSTed to the server.
- Change the login variables (the random username and password you plugged in) to the **_placeholders_** required by Hydra. These placeholders function as variables and Hydra will plug in its attempts into these placeholders/variables.

![image](https://github.com/GregKedrovsky/Hacking/assets/26492233/3a32dae8-e9b6-48d6-b73e-e002cca6b7f2)

### Capture a Failed Login String
- This is where most people get it wrong. You have to check in the webapp what a failed string looks like and put it in this parameter!

![image](https://github.com/GregKedrovsky/Hacking/assets/26492233/a9bc4a0d-338c-4f16-8820-bb324eb027f8)

### Final Syntax
```
hydra -L user_list.txt -P password_list.txt http://192.103.189.3 http-post-form "/login.php:login=^USER^&password=^PASS^&security_level=0&form=submit:Invalid credentials or user not activated!"
# the 3 required arguments in the http-post-form are colon-separated:             [1] <url>    [2] <form parameters>                                   [3]  <condition string>
```

### Additional Help
```
hydra -U http-post-form
# -U : service module usage details
```

## Hydra SSH Error

### Initial Hydra Command: 

```
# hydra -l root -P /usr/share/wordlists/metasploit/unix_passwords.txt ssh://192.168.61.129 -t 1 -V
```

### Error Message: 

```
[ERROR] could not connect to ssh://192.168.61.129:22 - kex error : no match for method kex algos: server [diffie-hellman-group-exchange-sha1,diffie-hellman-group1-sha1], client [curve25519-sha256,curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group18-sha512,diffie-hellman-group16-sha512,diffie-hellman-group-exchange-sha256,diffie-hellman-group14-sha256]
```

### Fix: 

You need to run three commands to work around this error: 
1. Update your system: `apt update && apt full-upgrade -y`
2. Confirm the kali-tweaks package is installed: `dpkg -l | grep kali-tweaks`
3. Run `kali-tweaks` and enable the SSH Wide Compatability module. If it is already enabled, disable it, exit, then go in again and enable it once more.
   - In the Main Menu of kali-tweaks, choose the first option: "Hardening."  
   - Select "SSH client" (arrow down to it, press spacebar to select)  
   - Arrow down (or tab) to "Apply" and hit ENTER.  
   - ENTER on "Ok" to Proceed (ENTER on the command line to continue)  
   - TAB to "Quit" and hit ENTER  

Try your Hydra command again and it should work.

You may want to go back into kali-tweaks and deselect that SSH configuration once you're finished so it doesn't jack up something else on your system. 

Workaround Source: https://youtu.be/fKVLVNaVXF0?si=mZOmHXwnHbISLeMQ




