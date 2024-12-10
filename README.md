# Hacking

This is a repo-in-process for my notes as I work through some training courses for three certifications: 
1. [eJPT](https://security.ine.com/certifications/ejpt-certification/) (done; passed the exam)
2. [PNPT](https://certifications.tcm-sec.com/pnpt/) (currently in process)
3. [OSCP](https://www.offsec.com/courses/pen-200/) (on the radar for 2025)

And, obviously, there some [THM](https://tryhackme.com/), [HTB](https://www.hackthebox.com/), and [Python](https://github.com/GregKedrovsky/Python) along the way.

This is an "organize as you go" project.
- I had no idea how to organize my notes, so I just started dumping in things I needed and needed to remember.
- Then when I had time and inspiration, I gave it a little structure and organization.
- Lather. Rinse. Repeat. 

## Links & Resources
- PayloadsAllTheThings: [repo](https://github.com/swisskyrepo/PayloadsAllTheThings) | [methodology](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Methodology%20and%20Resources) | [reverse shells](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md) | [powershell](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#powershell) | [cheat sheets](https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/hash-cracking/)
- Best reverse shell cheat sheet I've seen: [PenTest Monkey](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)
- Another [reverse shell cheat sheet](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md) (used it to set up a reverse shell via Groovy in Jenkins).
- [GTFOBins](https://gtfobins.github.io/): "Get The F**k Out" Binaries.
  - A curated list of Unix binaries that can be used to bypass local security restrictions in misconfigured systems.
  - These are legitimate *nix binaries that can be abused to break out restricted shells, escalate or maintain elevated privileges, transfer files, spawn bind and reverse shells, and facilitate the other post-exploitation tasks.
  - **Example:** Using [vi](https://gtfobins.github.io/gtfobins/vi/#sudo) to abuse sudo and get a root shell.
- [SecLists](https://github.com/danielmiessler/SecLists) by Daniel Miessler. Install in Kali:

```
apt -y install seclists
# installs to: /usr/share/seclists/
```
