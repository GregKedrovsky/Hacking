# Nmap

## Quick Reference

| Description | Syntax |
| ----------- | ------ |
| Initial Scan | `nmap -Pn [target ip]` |
| Faster Initial | `nmap -Pn --host-timeout 30s [target ip]` |
| Scan Open Ports | `nmap -Pn -sS -sV -sC -O -T4 -p [port,numbers] [target ip]` |

## Contents
- [Resources](#resources)
  - [Links](#links)
  - [Find scripts on your system](#find-scripts-on-your-system)
  - [Tips](#tips)
- [Host Discovery](#host-discovery)
  - [Standard](#standard)
  - [Scan Subnet for Open Ports](#scan-subnet-for-open-ports)
  - [TCP SYN Ping Scan](#tcp-syn-ping-scan)
  - [UDP Ping Scan](#udp-ping-scan)
  - [ICMP Ping Scan](#icmp-ping-scan)
  - [Problem: Firewalls](#problem-firewalls)
- [Port Scans](#port-scans)
  - [General Syntax](#general-syntax)
  - [Standard Go-To](#standard-go-to)
  - [Default Port Scan](#default-port-scan)
  - [Fast Scan](#fast-scan)
  - [Scan Ports](#scan-ports)
    - [Scan All Ports](#scan-all-ports)
    - [Scan for Open Ports](#scan-for-open-ports)
    - [Scan Specific Port](#scan-specific-port)
    - [Scan Multiple Ports](#scan-multiple-ports)
    - [Scan Range of Ports](#scan-range-of-ports)
  - [TCP Connect Scan](#tcp-connect-scan)
  - [UDP Port Scan](#udp-port-scan)
  - [UDP Top Port scan](#udp-top-port-scan)
  - [Version Scan](#version-scan)
  - [Operating System Scan](#operating-system-scan)
  - [Defaults Script Scan](#defaults-script-scan)
  - [Aggressive Scan](#aggressive-scan)
- [Additional Options](#additional-options)
  - [Increase Speed](#increase-speed)
    - [Timing Templates](#timing-templates)
    - [Option: min-rate](#option-min-rate)
  - [Output to File](#output-to-file)
  - [Scan Delay](#scan-delay)
  - [Slow Target Response](#slow-target-response)

----
## Resources

### Links
- [Reference Guide](https://nmap.org/book/man.html)
- [Host Discovery](https://nmap.org/book/man-host-discovery.html)
- [NSE Script List](https://nmap.org/nsedoc/scripts/)

### Find scripts on your system
```
locate *.nse
# or in Kali
ls -l /usr/share/nmap/scripts | grep ftp-*  # (or smb, http, etc.)
```

### Tips
- **Combine Options:** `nmap -sU -sV -sC [target IP]` is the same as `nmap -sUVC [target IP]`
- **Verbosity:** Increase the verbosity of the output with `-v`, and more with `-vv`
- **Status:** Hit `ENTER` while nmap is doing its thing to see the status (percentage done).

----
## Host Discovery

### Standard
```
nmap -sn  -v -T4 [target ip subnet]/[CIDR]
```
- `-sn` : (scan no) host discovery, no port scans (because we don't want to scan the devices on the network right now, we want to FIND them)
- `-v` : verbosity
- `-T4` : increase speed (1-5, with 5 as highest)

### Scan Subnet for Open Ports
```
nmap --open -T4 [target ip subnet]/[CIDR]
nmap --open -T4 10.4.20.0/20
```
- `--open` : Only show hosts that have open ports, and only show the open ports for those.

### TCP SYN Ping Scan
```
nmap -sn -PS 21,22,25,80,445,3389,8080 -T4 [target ip subnet]/[CIDR]
```
- `-PS [port list]` : TCP SYN Ping
- Add the most common ports for Linux and Windows to save time

### UDP Ping Scan
```
nmap -sn -PS21,22,25,80,445,3389,8080 -PU137,138 -T4 [target ip subnet]/[CIDR]
```
- `-PU [port list]` : UDP Ping Scan.  It sends a UDP packet (usually empty) to the given ports.
- Upon hitting a closed port on the target machine, the UDP probe should elicit an ICMP port unreachable packet in return. This signifies to Nmap that the machine is up and available.

### ICMP Ping Scan
> This is a host discovery scan than only sends ICMP Echo Requests (just like when we used the ping command). 
```
nmap -sn -PE  [target ip subnet]/[CIDR]
```
- `-sn` :  Ping Scan - disable port scan
- `-PE` :  Nmap sends an ICMP type 8 (echo request) packet to the target IP addresses, expecting a type 0 (echo reply) in return from available hosts.

### Problem: Firewalls
> Most firewalls today block these kinds of requests. So, you could try a variant...
```
nmap -sn -PE [target ip subnet]/[CIDR] --send-ip
```
- `--send-ip` : send packets via raw IP sockets rather than sending lower level Ethernet frames

## Port Scans
> These scans need to be run as root.
> 
### General Syntax
```
nmap [Scan Type(s)] [Options] {target specification}
```
- If you are dealing with a Windows system and you get a `filtered` STATE in your response, you can be assured the target system has a **_firewall_**.
- If you are dealing with a Windows system and you get a `closed` STATE in your response, then Windows Firewall is **_not active_** or there are **_not rules_** for particular port that are intercepting traffic.
- `-v` provides some verbosity. `-vv` provides more verbosity.
- `--reason` : display the reason a port is in a particular state

### Standard Go-To
```
#TCP Ports:
nmap -sS -T4 -p- -A -vv [target ip]
nmap -sS -T4 -p- -A -vv --reason [target ip]

#UDP Ports: 
nmap -sU -T4 -A -vv [target ip]
nmap -sU -T4 -A -vv --reason [target ip]
```

### Default Port Scan
```
nmap [target ip]
# or
nmap -Pn [target ip]
```
- Default Scan: If you are running as root then nmap will run a SYN port scan (`-sS` : stealth Scan option) by default (without you having to type it in).
  -  That means nmap will first perform host discovery automatically (ping scan)
  -  Then (based on the port range, if you specified one) nmap will send a SYN packet to the target ports.
  -  If it gets a SYN-ACK in return, the port is open. All else, port considered closed.
- Default Ports: (when none are specified) The 1000 most commonly used ports.
- Problem: Because it's performing host discovery with a ping sweep, if the target is blocking ICMP requests, then it won't do the port scan since the host was not "discovered."
  - Solution: `-Pn` : Ping no. Disable the host discovery ping sweep and go right to the port scan.

### Fast Scan
```
nmap -Pn -F [target ip]
```
- `-F` : Scans 100 of the most commonly used ports (instead of the default 1000)

### Scan Ports

#### Scan All Ports
```
nmap -Pn -p- [target ip]
```

#### Scan for Open Ports
```
nmap [target ip or ip/CIDR subnet] --open
```
- `--open`: Only show hosts that have open ports, and only show the open ports for those

#### Scan Specific Port
```
nmap -Pn -p 80 [target ip]
```

#### Scan Multiple Ports
```
nmap -Pn -p 80,445,3389 [target ip]
```

#### Scan Range of Ports
```
nmap -Pn -p 1-65535 [target ip]
```

### TCP Connect Scan
> This is the default scan for an unprivileged/non-root user because you have to be root in order to have "raw packet privileges"
- This scan completes the TCP three-way handshake.
  - It sends an TCP packet with the SYN flag set.
  - If it receives a TCP packet with the SYN-ACK flag set...
  - It sends a TCP packet with the ACK flag set.
- **_This is very loud on the network_**. But it is also very accurate.
```
nmap -Pn -sT [target ip]
```
- `-sT` : TCP Connect Scan.

### UDP Port Scan
```
nmap -Pn -sU  [target ip]
nmap -Pn -sU -p 53,137,138,139 [target ip]
```
- `-sU` : activates a UDP scan (instead of the default TCP scan)
- UDP scan works by sending a UDP packet to every targeted port.
- If an ICMP port unreachable error (type 3, code 3) is returned, the port is closed.
- Other ICMP unreachable errors (type 3, codes 1, 2, 9, 10, or 13) mark the port as filtered.
- Occasionally, a service will respond with a UDP packet, proving that it is open.
- If no response is received after retransmissions, the port is classified as open|filtered.
  - This means that the port could be open, or perhaps packet filters are blocking the communication.
  - `-sV` : Version detection can be used to help differentiate the truly open ports from the filtered ones.

### UDP Top Port scan
```
nmap -sU --top-ports 25 --open [target ip]
```
- This will only scan the top 25 ports, not the entirety. and only the open ports. Faster.

### Version Scan
```
nmap -Pn -sV [target ip]
```
- `-sV` : show the version of the service running on the open port.

### Operating System Scan 
```
nmap -Pn -O [target ip]
```
- `-O` : This will attempt to ID the O/S running on the target system

### Defaults Script Scan
```
nmap -Pn -sV -O -sC [target ip]
```
- `-sC` : Runs the default (safe) Nmap scripts on the open ports (equivalent: `--script=default`).

### Aggressive Scan
```
nmap -Pn -A [target ip]
```
- It combines the following into one switch: `-A`
- `-sV` : Version detection
- `-sC` : Script scanning
- `-O` : OS detection
- `–traceroute` : Traceroute

----
## Additional Options

### Increase Speed

#### Timing Templates
```
nmap -Pn -T5 -sV -O -sC [target ip] -v
```
- Nmap's timing templates: T<0-5>: Set timing template (higher is faster)
- `-T0` : Paranoid
- `-T1` : Sneaky
- `-T2` : Polite	
- `-T3` : Normal
- `-T4` : Aggressive
- `-T5` : Insane

#### Option: min-rate
```
nmap -sV --min-rate 1000 -p- 10.129.128.223
```
- This is used to specify the minimum number of packets Nmap should send per second
- It speeds up the scan as the number goes higher

### Output to File
```
nmap [target IP address] -oN output.txt
```
- `-oN` : output normal
- `-oX` : output XML (allows you to import your output into a framework like MetaSploit).
- `-oS` : output ScriptKiddie
- `-oG` : output Grepable
- `-oA` : output All (all formats)

### Scan Delay
```
nmap -sS -sV -F --scan-delay 5s [target ip]
```
- `--scan-delay time` or `--max-scan-delay time` :  Adjust delay between probes.
- You can append `ms` (milliseconds), `s` (seconds), `m` (minutes), or `h` (, or hours). 

### Slow Target Response
- You can also add `--host-timeout [time]` to give up on slow target hosts.
- The time parameter is specified in seconds by default.
- You can append `ms` (milliseconds), `s` (seconds), `m` (minutes), or `h` (, or hours).
- TIP: Set it to `30s` if you are working on a large network and/or with a large group of IPs.

