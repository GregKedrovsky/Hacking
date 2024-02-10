# Nmap Common

## Contents
- [Common Switches](#common-switches)
- [Most Common Commands](#most-common-commands)
  - [Target Selection](#target-selection)
  - [Port Selection](#port-selection)
  - [Scan Types](#scan-types)
  - [Service and OS Detection](#service-and-os-detection)
  - [Output Formats](#output-formats)
- [Nmap Scripting Engine](#nmap-scripting-engine)
  - [General Usage & Syntax](#general-usage--syntax)
  - [Useful NSE Scripts](#useful-nse-scripts)
  - [Script Categories](#script-categories)
  - [Examples & Reference](#examples--reference)

## Common Switches

| Switch | Description |
| :------: | -------------- |
|  `-Pn`  | Ping not; disable host discovery |
|  `-sS`  | scan SYN (stealth; default) |
|  `-sT`  | scan TCP |
|  `-sU`  | scan UDP |
|  `-sV`  | scan Versions (on specified ports) |
|  `-T[0-5]`  | [Timing Templates](https://nmap.org/book/performance-timing-templates.html) (higher is faster) |
|  `-p-`  | Ports, scan all |
|  `-p [csv]`  | Ports, scan specified |
|  `-O`  | OS Fingerprinting |
|  `-A`  | Aggressive Scan (multiple functions; noisy!) |
|  `-vv`  | Very Verbose |
|  `--reason`  | Reason a port is in said state |
|  `--script=vuln`  | NSE Scripts, RE: vulnerabilities |

## [Most Common Commands](https://www.yeahhub.com/top-30-basic-nmap-commands-beginners/)
> The following is a list and description of the 30 most common basic commands in nmap (for beginners).

### Target Selection

|  #  | Description | Syntax |
| :-: | ----------- | ------ |
| 01 | Scan a single IP | `nmap 192.168.20.128` |
| 02 | Scan a host | `nmap %%www.example.com%%` |
| 03 | Scan a range of IPs | `nmap 192.168.20.120-128` |
| 04 | Scan a subnet | `nmap 192.168.20.2/24` |
| 05 | Scan targets from text file | `nmap -iL ips.txt` |

### Port Selection

|  #  | Description | Syntax |
| :-: | ----------- | ------ |
| 06 | Scan a single port| `nmap -p 22 192.168.20.128` |
| 07 | Scan a range of ports| `nmap -p 1-100 192.168.20.128` |
| 08 | Scan 100 common ports| `nmap -F 192.168.20.128` |
| 09 | Scan all ports| `nmap -p- 192.168.20.128` |
| 10 | Specify UDP or TCP scan| `nmap -p U:137,T:139 192.168.20.128` |

### Scan Types

|  #  | Description | Syntax |
| :-: | ----------- | ------ |
| 11 | Scan using TCP connect| `nmap -sT 192.168.20.128` |
| 12 | Scan using TCP SYN scan| `nmap -sS 192.168.20.128` |
| 13 | Scan UDP ports| `nmap -sU -p 123,161,162 192.168.20.128` |
| 14 | Scan, Ignore Discovery (Ping not)| `nmap -Pn -F 192.168.20.128` |

### Service and OS Detection

|  #  | Description | Syntax |
| :-: | ----------- | ------ |
| 15 | Detect OS and Services| `nmap -A 192.168.20.128` |
| 16 | Standard service detection| `nmap -sV 192.168.20.128` |
| 17 | Aggressive service detection| `nmap -sV –version-intensity 5 192.168.20.128` |

### Output Formats

|  #  | Description | Syntax |
| :-: | ----------- | ------ |
| 18 | Save as normal file| `nmap -oN result.txt 192.168.20.128` |
| 19 | Save as XML| `nmap -oX resultxml.xml 192.168.20.128` |
| 20 | Save as Grepable| `nmap -oG formattable.txt 192.168.20.128` |
| 21 | Save in all formats| `nmap -oA allformats 192.168.20.128` |

## [Nmap Scripting Engine](https://nmap.org/nsedoc/scripts/)

### General Usage & Syntax

|  #  | Description | Syntax |
| :-: | ----------- | ------ |
| 22 | Scan using default safe scripts| `nmap -sV -sC 192.168.20.128` |
| 23 | Get help for a script| `nmap –script-help=ssl-heartbleed` |
| 24 | Scan using a specific script| `nmap -sV -p 443 -script=ssl-heartbleed 192.168.20.133` |
| 25 | Update script database| `nmap –script-updatedb` |

### Useful NSE Scripts

|  #  | Description | Syntax |
| :-: | ----------- | ------ |
| 26 | Scan for UDP DDOS reflectors| `nmap -sU -A -PN -n -pU:19,53,123,161 -script=ntp-monlist,dns-recursion,snmp-sysdescr 192.168.20.2/24` |
| 27 | Gather page titles from HTTP Servers| `nmap –script=http-title 192.168.20.128` |
| 28 | Get HTTP headers of web services| `nmap –script=http-headers 192.168.20.128` |
| 29 | Find web apps from known paths| `nmap –script=http-enum 192.168.20.128` |
| 30 | Find exposed Netbios servers| `nmap -sU –script nbtstat.nse -p 137 192.168.20.128` |

### [Script Categories](https://subscription.packtpub.com/book/networking-and-servers/9781782168317/1/ch01lvl1sec11/script-categories)

|  Category  | Description |
| :--------: | ----------- |
|  **auth**  | NSE scripts related to user authentication. |
|  **broadcast**  | A very interesting category of scripts that use broadcast petitions to gather network information. |
|  **brute**  | A category for scripts that help conduct brute-force password auditing. |
|  **default**  | Scripts executed when a script scan is executed (-sC). |
|  **discovery**  | Scripts related to host and service discovery. |
|  **dos**  | Scripts related to denial-of-service attacks. |
|  **exploit**  | Scripts used to exploit security vulnerabilities. |
|  **external**  | This category is for scripts depending on a third-party service. |
|  **fuzzer**  | NSE scripts focused on fuzzing. |
|  **intrusive**  | A category for scripts that might crash something or generate a lot of network noise. Scripts that system administrators may consider intrusive go here. |
|  **malware**  | A category for scripts related to malware detection. |
|  **safe**  | Scripts that are considered safe in all situations. |
|  **version**  | Scripts for advanced version detection. |
|  **vuln**  | Scripts related to detecting and exploiting security vulnerabilities. |

### [Examples & Reference](https://www.tecmint.com/use-nmap-script-engine-nse-scripts-in-linux/)

- To find the scripts on your system:
```
locate *.nse
# or
find /usr/share/nmap/ "*.nse"
```

- The syntax for enabling scripts is as follows:
```
namp -sC [target]     #load default scripts
# or
nmap --script filename|category|directory|expression,...[target]
```

- Syntax to perform a scan with most of the default scripts, use the `-sC` flag or alternatively use `--script=default` as shown here:
```
nmap -sC [target IP or domain name]
# or
nmap --script=default [target IP or domain name]
# or
nmap --script default [target IP or domain name]
```

- Examples:
```
nmap --script smb-vuln* -p 445 10.10.10.4

nmap -sV --script=smb-enum-shares -p 445 10.10.10.4

nmap --script "http-*" 
# quotes to protect the wildcard from the shell

nmap --script +ms-sql-config

nmap --script-help ftp-anon

nmap --script-help default

nmap -sC example.com
# simple script scan using the default set of scripts

nmap -sn -sC example.com
# script scan without a port scan; only host scripts are eligible to run

nmap -Pn -sn -sC example.com
# script scan without host discovery or a port scan.
# All hosts are assumed up and only host scripts are eligible to run.

## Common Go-To Example:
nmap -Pn -p [csv,ports,to,scan] --script safe,discovery,vuln,exploit -T4 -vv --reason [target IP]
```
