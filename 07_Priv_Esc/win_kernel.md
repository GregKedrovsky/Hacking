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
- Module: `post/multi/recon/local_exploit_suggester`
- This is post-exploitation module. Run it after you get an unprivileged session in the MSF. Set it to run on that session.

## Manual Kernel Exploit

### Tool: [Windows-Exploit-Suggester](https://github.com/AonCyberLabs/Windows-Exploit-Suggester)
- This tool compares a target's patch levels against the Microsoft vulnerability database in order to detect potential missing patches on the target. It also notifies the user if there are public exploits and Metasploit modules available for the missing bulletins.

### Steps
- Per the instructions on the GitHu README.md...
- Step 1: Clone the repo onto your local (kali attack) machine.
- Step 2: run the update (./windows-exploit-suggester.py --update)
- Step 3: run it with the --database and --systeminfo options (see below)

### System Info
- The system info you need to copy into a text file (e.g., win7sp1-systeminfo.txt) is obtained by running `systeminfo` on the Windows target via a cmd (command shell) session.
- The output from that `systeminfo` command is what you need to put into the `systeminfo.txt file`.
  - Highlight it and copy it all (from Host Name at the top to Network Card(s) at the bottom).
  - Paste into your favorite text editor and save it as a .txt file.

### Run the Update
```
> $ ./windows-exploit-suggester.py --update
```
- This creates your database file you'll use in the next step (e.g., `writing to file 2021-12-26-mssb.xls`)

### Run the Suggester
```
> $ ./windows-exploit-suggester.py --database 2021-12-26-mssb.xls --systeminfo ~/Desktop/systeminfo.txt
```

### The Results
- The results are a list of potential vulnerabilities the script discovered.
- The exploits listed at the very top are the ones that have the highest chances of success.
- Check out the green + with an explanation of the symbols:
  - `[E]` An Exploit Database Proof of Concept exists for the vuln/exploit
  - `[M]` A Metasploit module exists for the vuln/exploit
  - `[*]` There is a missing bulletin regarding the vuln/exploit
- Google search on exploits you think might work, and try them out. 

### Recommendation
- Experiment with all of this (and the list of exploits from Windows-Exploit-Suggester) on your own home lab.
- Set up various target systems with different versions of Windows.
- Test drive and practice.

