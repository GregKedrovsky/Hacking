# CME | CrackMapExec

Example, SSH Brute Force: 
crackmapexec ssh 10.10.11.166 -u michael -p /usr/share/wordlists/rockyou.txt

CrackMapExec: 
https://www.kali.org/tools/crackmapexec/
CrackMapExec (CME) is an open-source hacking tool that automates gathering information, executing advanced password attacks, and performing post-exploitation activities like lateral movement.
It’s designed to be a “Swiss Army knife” for targeting Windows Active Directory environments and has been used in many real-world attacks.

Good cheat sheet: https://www.stationx.net/crackmapexec-cheat-sheet/

General CrackMapExec Syntax and Options:

crackmapexec [runtime options] <service> [options] [-M module] [-o module options] <target ip>

<service> : CrackMapExec can interact with various services running on the target machine. Each can be used to perform specific tasks related to enumeration, exploitation, or lateral movement.
winrm
ldap
ssh
rdp
mssql
FTP
smb

[options] - Options are specific to the service you are targeting, but there are common ones you will see.
-u : for username
-p : for password
-h : to get help for that module
-x : COMMAND to execute a command on the target
-X : PS_COMMAND to execute a PowerShell command-L list modules available for service

Each service has a help menu: crackmapexec [service] -h
For SMB: crackmapexec smb -h
For SSH: crackmapexec ssh -h

Each service has multiple modules you can use: crackmapexec [service] -L
This will list out the modules for that service.
