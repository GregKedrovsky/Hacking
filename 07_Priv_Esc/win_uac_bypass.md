# Privilege Escalation: Bypassing UAC
> Bypassing UAC With UACMe

## Contents
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)

## User Account Control (UAC)
- UAC is a Windows security feature introduced in Windows Vista that is used to prevent unauthorized changes from being made to the operating system.
- UAC is used to ensure that changes to the operating system require approval from the administrator or a user account that is part of the local administrators group.
- A non-privileged user attempting to execute a program with elevated privileges will be prompted with the UAC credential prompt (admiun password), whereas a privileged user will be prompted with a consent prompt (image below).
- Attacks can bypass UAC in order to execute malicious executables with elevated privileges.

## The Problem: UAC Pop-Up
- The problem you run into is when you're running a remote shell or a meterpreter session on a target machine, and you try to run a program with elevated privileges.
- A dialog box will pop up on the target system system, but b/c you are on the command line, you won't see and won't have any way to acknowledge it.
- Therefore you need a way to bypass that.

## Bypassing UAC
- In order to successfully bypass UAC we will need to have access to a user account that is a part of the local administrators group on the Windows target system.
- UAC allows a program to be executed with administrative privileges, consequently prompting the user for confirmation.
- UAC has various integrity levels ranging from low to high. If the UAC protection level is set below high, Windows programs can be executed with elevated privileges without prompting for user confirmation.
- There are multiple tools and techniques that can be used to bypass UAC, however the tool and technique used will depend on the version of Windows running on the target system.

## Tool: [UACMe](https://github.com/hfiref0x/UACME)
> Bypassing UAC With UACMe. Look over the README.md documentation.
- UACMe is an open-source, robust privilege escalation tool developed by @hfire0x. It can be used to bypass Windows UAC by leveraging various techniques.
- The UACMe GitHub repo contains a very well documented list of methods that can be used to bypass UAC on muiltiple versions of Windows ranging from Windows 7 to Window 11.
- It allows attackers to execute malicious payloads on a Windows target with admin/elevated privileges by abusing the inbuilt Windows AutoElevate tool.
- The UACMe GitHub repo has more than 60 exploits that can be used to bypass UAC depending on the version of Windows running on the target.
- NOTE: When selecting a Key, you need to find one that shows "unfixed" in the version of Windows your target machine is running.

## Practical: UACMe

### Scenario
- Unprivileged meterpreter session from system exploit via MSF.
- You get a shell on the system, you enumerate, and try to change the administrator password:
```
sysinfo
# This gives you O/S info (very important)
# Meterpreter: x86/windows (32-bit)

pgrep explorer
# This searches for the explorer process number
# Note the number (process ID for the explorer.exe process)

migrate [pgrep number]
# This should give you a 64-bit meterpreter session

sysinfo
# Meterpreter: x64/windows; good to go

getuid
#You are logged on as "admin" (NOT "administrator"--different). Need priv esc.

getprivs
# Shows you the privileges assigned to the current user
# Indicates an "unprivileged" (non-administrator) account

shell
# Opens a shell session with the current user's creds
```
- In the shell:
```
> net user
  # Shows two accounts: admin and administrator (plus Guest)

> net localgroup administrators
  # Shows admin is part of the administrators group
  # This means that this user can execute with administrator privileges (BUT via UAC)

> net user admin password123
  # Just an example: try to set a new password for the admin account
  # Returns: "System error 5 has occurred. Access is denied."
  # That's UAC blocking the elevated priv. command
```


