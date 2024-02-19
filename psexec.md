# What is PsExec?

PsExec is a lightweight telnet-replacement developed by Microsoft that allows you to execute processes on remote Windows systems using any user's credentials.
- PsExec authentication is performed via SMB.
- We can use the PsExec utility to authenticate with the target system legitimately and run arbitrary commands or launch a remote command prompt.
- It is very similar to RDP, however, instead of controlling the remote system via GUI, commands are sent via CMD.

## SMB Exploitation with PsExec: 
- In order to utilize PsExec to gain access to a Windows target, we need to identifiy legitimate user accounts first, along with their respective passwords or password hashes. 
- This can be done by leveraging various tools and techniques. 
- However, the most common technique will involve performing an **_SMB login brute-force attack_**.
- We can narrow down our brute-force attack to only include common Windows user accounts like `Administrator` (which is constant across all Windows installations).
- After we have obtained a legitimate user account and password, we can use the credentials to authenticate with the target system via `PsExec` and execute arbitrary system commands or obtain a reverse shell. 

## psexec.py

PsExec is a Windows executable but we are in Linux (Kali, for example), so in Linux you need... `psexec.py` Python program.
```
psexec.py Administrator@[target ip]
# or execute a command:
psexec.py Administrator@[target ip] [command to execute; e.g., cmd.exe]
```
- You'll be prompted for the password
- If this gives you a command prompt on the target system: `whoami`, etc.
