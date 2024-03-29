# Active Recon
> Active info gathering, in the context of a pentest, refers to the phase of the assessment where the tester actively interacts with the target system or network to collect data and identify potential vulnerabilities. This phase involves techniques that go beyond passive reconnaissance (where information is gathered without directly interacting with the target) and may include activities such as scanning, probing, and direct interaction with network services. 

Active information gathering will feed the rest of the pentest. It is essesntial. You need to be as thorough as possible.

## Contents
- [Network Mapping](#network-mapping)
- [Tools](#tools)
    - [Nmap](#nmap)
    - [URL Enum](#url-enum)
    - [DNS Enum](#dns-enum)
    - [SMTP](#smtp)
    - [SNMP](#snmp)

## Network Mapping

### What is "Network Mapping"?
- After collecting information about a target organization during the passive information gathering stage, a penetration tester typically moves on to active information gathering phase which involves discovering hosts on a network, performing port scanning, and enumeration.
- Every host connected to the Internet or to a private network must have a unique IP address that identifies it on its network.
- How can a pentester determine what hosts, within an in-scope network, are online? What ports are open on the active hosts? What operatings systems are the active hosts running?
  - Answer: Network Mapping
- "Network mapping," in the context of a pentest, refers to the process of discovering and identifying devices, hosts, and network infrastructure elements within a target network.
- Pentesters use network mapping as a crucial initial step to gather information about the network's layout, understand its architecture, and identify potentional entry points for further exploitation. 
 
### Why map a network?
- A company asks you (your company) to perform a pentest and the following address block is consdiered in scope: 200.200.0.0/16
- A sixteen-bit long netmask means the network could contain up to 216 (65536) hosts with IP addresses in the 200.200.0.0 - 200.200.255.255 range.
- The first job for the pentester will involve determining which of the 65,536 IP addresses area assigned to a host, and which of these hosts are online and active. 

### Network Mapping Objectives: 

#### Discovery of Live Hosts:
- Identify active devices and hosts on the network.
- This involves determining which IP addresses are currently in use.

#### Identification of Open Ports and Services:
- Determining which ports are open on the discovered hosts and identifying the services running on those ports.
- This information helps pentesters understand the attack surface and potential vulnerabilities. 

#### Network Topology Mapping: 
- Creating a map or diagram of the network topology, including routers, switches, firewalls, and nother network infrastructure elements. 
- Understanding the layout of the network assists in planning further penetration testing activities.

#### Operating System Fingerprinting: 
- Determining the operating systems running on discovered hosts. 
- Knowing the operating system helps pentesters tailor their attack stategies to target vulnerabilities specific to that o/s.

#### Service Version Detection: 
- Identifying specific versions of services running on open ports.
- This information is crucioal for pinpointing vulnerabilities associated with particular service versions. 

#### Identifying Filtering and Security Measures: 
- Discovering firewalls, intrusion prevention systems, and other security measures in place. 
- This helps pentesters understand the network's defenses and plan their approach accordingly. 

## Tools

### nmap

#### The Tool for Network Mapping: Nmap
- Nmap, or Network Mapper, is an open-source scanning tool used for discovering hosts and services on a computer network, finding open ports, and identifying potential vulnerabilities.
- It is a powerful and versatile tool that has become a standard in the tool kit of security professionals, network admins, and pentesters.
- Nmap offers a range of features and functionalities that make it a valuable tool in various network security contexts.

#### Nmap Funcationality
- **Host Discovery:** Nmap can identify live hosts on a network using techniques such as ICMP echo requests, ARP requests, or TCP/UDP probes.
- **Port Scanning:** Nmap can perform various types of port scans to discover open ports on target hosts.
- **Service Version Detection:** Nmap can determine the versions of sewrvices running on open ports. This information helps in understanding the software stack and potential  vulnerabilities associated with specific versions.
- **Operating System Fingerprinting:** Nmap can attempt to identify the operating systems of target hosts based on characteristics observed during the scanning process. 

### URL Enum
> URL enumeration makes a list of URLs in a domain, often showing hidden files and directories.
- This is especially important in web application pentesting.
- NOTE: This is an aggressive process.
- Tool: [FFUF](https://www.kali.org/tools/ffuf/) (Fuzz Faster U Fool)

### DNS Enum
> DNS enumeration is like an aggressive DNS lookup (think whois on steroids).
- This is active recon and it will take a long time.
- It barfs out a butt-ton of information.
- Tool: [dnsenum](https://www.kali.org/tools/dnsenum/)
- Example syntax:
```
dnsenum –enum google.com
```

### SMTP
> Simple Mail Transfer Protocol (SMTP): A vintage email sending protocol. Runs on port 25. No much built in security.
- You can gather info on SMTP by telnetting to the service port (25) and grabbing the banner information and then using [VRFY and/or EXPN](https://cr.yp.to/smtp/vrfy.html) to gather more info.
- Example sytax:
```
telnet example.server.com 25
# once connected, type:
VRFY [username]
# or
EXPN [user_alias]
```

#### Exploits:
- SMTP exploits are usually associated with a vulnerable version.
- Connect (by telnet). Grab banner info (`VRFY`, `EXPN`).
- This should give you a clue as to the SMTP version on your target.

### SNMP
> SNMP stands for "Simple Network Management Protocol." SNMP is used to collect data related to network changes or to determine the status of network-connected devices.
- You can use SNMP to gather information on a system if you have access to the system and you know the “read” community string (which is often: public).
- Tools: [snmpenum](https://www.kali.org/tools/snmpenum/) and [snmpwalk](http://www.net-snmp.org/wiki/index.php/Snmpwalk)
- Example syntax for snmpwalk, assuming "public" as the community string
```
snmpwalk -c public -v1 192.168.1.1
```
