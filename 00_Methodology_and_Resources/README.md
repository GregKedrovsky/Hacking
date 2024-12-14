# Methodology & Resources

## Contents
- [Five Stages of Ethical Hacking](#five-stages-of-ethical-hacking)
- Basic Networking
  - [Copy Tables from Here](https://nix.kedrovsky.net/method_7_notes)
  - OSI Model
  - TCP/IP Model
  - OSI + TCP/IP 

## Five Stages of Ethical Hacking

This methodology does not change (not for different pentests, not for much of anything).
- Your tools may change. Your exploits will change.
- But these basic steps are the methodology of a hack, always. 

### 1. Reconaissance (active & passive)
- **Passive:** LinkedIn, Facebook, etc. (you're not engaging with any system or network actually owned by your target)
- **Active:** You engage with the target system/network directly.

### Scanning & Enumeration (nmap, nessus, nikto, etc.)
- **Scan the Network:** looking for live nodes/machines
- **Scan the Machines:** looking for open ports and services on those ports
- **Enumeration:** List out what you found in the scans, dig into them (research things, Google, etc.), see if you can find anything of value to exploit and gain a foothold.

### Exploitation (gaining access)
- Run an exploit against the target (vulnerability).
- Try to gain access and get a foothold. 

### Persistence (maintaining access)
- Now your process repeats: scan & enumerate again from your foothold in the network.
- Put something in place that will give you a persistent foothold without having to hack your way back in again if the target user reboots, etc. 

### Covering Your Tracks
- Delete any logs you may leave behind.
- Delete any tools or malware you uploaded. 
- Delete any accounts you may have created during your foray. 
- Clean up. 

![image](https://github.com/user-attachments/assets/f0e4fc5b-3d1c-4385-95cc-87fbf2c585a4)
