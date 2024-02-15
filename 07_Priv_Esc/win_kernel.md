# Privilege Escalation: Windows Kernel
> Kernel Exploits can give us Privilege Escalation.

## Contents 
- [](#)

## Windows Kernal
> A "kernel" is a computer program (or set of programs) that is the core of an operating system. A kernel has complete control over every resource and piece of hardware on the system. A kernel acts as a translation layer between hardware and software, and it facilitates the communication between these two layers.
- Windows NT is the kernel that comes pre-packaged with all versions of Microsoft Windows, and it operates as a traditional kernel with a few exceptions based on user design and philosophy. It consists of two main modes of operation that determine access to system resources and hardware:
  - **User Mode:** Programs and services running in user mode have limited access to system resources and functionality.
  - **Kernel Mode:** Unrestricted access to system resources and functionality with the added functionality of managing devices and system memory.
 
## Windows Kernel Exploitation
> Kernel exploits on Windows typically target vulnerabilities in the Windows kernel to execute arbitrary code in order to run privileged system commands or to obtain a system shell. This process will differ based on the version of Windows being targeted and the kernel exploit being used.
- Privilege escalation on Windows systems will typically follow the following methodology:
  - FIRST: Identify kernel vulnerabilities.
  - SECOND: Download, compile, and transfer kernel exploits onto the target system.
 
## Tools, Overview

### [Windows-Exploit-Suggester](https://github.com/AonCyberLabs/Windows-Exploit-Suggester) (Python)
> This tool compares a target's patch levels against the Microsoft vulnerability database in order to detect potential missing patches on the target. It also notifies the user if there are public exploits and Metasploit modules available for the missing bulletins.

### [Windows-Kernel-Exploits](https://github.com/SecWiki/windows-kernel-exploits)
> A collection of Windows kernel exploits sorted by CVE and MS Vulnerability ID.
- Example: [Local Privilege Escalation - MS16-135](https://github.com/SecWiki/windows-kernel-exploits/tree/master/MS16-135)

### MetaSploit
> You can also use MSF modules to do this (i.e., automatically instead of manually).

## Something

