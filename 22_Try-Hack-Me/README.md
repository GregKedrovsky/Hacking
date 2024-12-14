# Try Hack Me

Box write-ups, notes, and walk-throughs...

## TCM (The Cyber Mentor) | Linux PrivEsc Arena

If you are working through TCM's [Linux Privilege Escalation](https://academy.tcm-sec.com/p/linux-privilege-escalation) course, you may get the following error when you try to ssh into his [Linux PrivEsc Arena](https://tryhackme.com/r/room/linuxprivescarena) on TryHackMe: 

```
# ssh 10.10.16.218    # That was IP I was given; yours may differ
Unable to negotiate with 10.10.16.218 port 22: no matching host key type found. Their offer: ssh-rsa,ssh-dss
```

- SSH-RSA and SSH-DSS are not recommended due to security weaknesses, compatibility issues, and planned deprecation.
- However, you still need to use them to get into TCM's Linux PrivEsc Arena.

### Workaround:

Your workaround is to explicity tell ssh to use the `ssh-rsa` algorithm: 
```
ssh -o HostKeyAlgorithms=ssh-rsa TCM@[target ip]
```
- This tells ssh to use the RSA host key algorithm when verifying the host key during connection establishment.
- That should get you in. 

### Automate with a Shell Script: 

If you don't want to type all of that every time you need to log into the lab, create the following shell script.
- This shell script uses zsh because that's the default terminal in Kali.
- If you need a different shell (like Bash), then just change the first line, the shebang.
- You can name this file whatever you want, but you need to modify perms so it is executable (e.g., `chmod 755 [scriptname]`).

```
#!/usr/bin/env zsh

if [ $# -ne 1 ]; then
    echo "\nParameters: $# (and we need at least one)"
    echo "\n    Usage: $0 <remote ip address>"
    exit 1
fi

sshpass -p "Hacker123" ssh -o HostKeyAlgorithms=ssh-rsa TCM@$1
```

- With this script, all you have to do is run the script name with one parameter: the IP of the target machine.
- Since the IP of the target machine will likely change with each session, this seems to be the easiest workaround for me.

