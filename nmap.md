# Nmap

## Quick Reference

| Description | Syntax |
| ----------- | ------ |
| Initial Scan | `nmap -Pn [target ip]` |
| Scan Open Ports | `nmap -Pn -sS -sV -sC -O -T4 -p [port,numbers] [target ip]` |

## Contents
- []()

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
**Combine Options:** `nmap -sU -sV -sC [target IP]` is the same as `nmap -sUVC [target IP]`

**Verbosity:** Increase the verbosity of the output with `-v`, and more with `-vv`

**Status:** Hit `ENTER` while nmap is doing its thing to see the status (percentage done).

## Host Discovery

### Standard:
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

### TCP SYN Ping Scan: 
```
nmap -sn -PS 21,22,25,80,445,3389,8080 -T4 [target ip subnet]/[CIDR]
```
- `-PS [port list]` : TCP SYN Ping
- Add the most common ports for Linux and Windows to save time

### UDP Ping Scan: 
```
nmap -sn -PS21,22,25,80,445,3389,8080 -PU137,138 -T4 [target ip subnet]/[CIDR]
```
- `-PU [port list]` : UDP Ping Scan.  It sends a UDP packet (usually empty) to the given ports.
- Upon hitting a closed port on the target machine, the UDP probe should elicit an ICMP port unreachable packet in return. This signifies to Nmap that the machine is up and available.

### ICMP Ping Scan: 
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

### Default scan, Ping No
```
nmap -Pn [target IP or CIDR notation network]
```
- This disables host discovery. 
- It causes Nmap to attempt the requested scanning functions against every target IP address specified. 
- Nmap performs the requested functions as if each specified target IP is active.

### Scan All Ports
```
nmap -Pn -p- [target IP address]
```

### Scan for Open Ports
```
nmap [target IP or IP/CIDR subnet] --open
```
- `--open`: Only show hosts that have open ports, and only show the open ports for those

### Scan Specific Port
```
nmap -Pn -p 80 [target IP address]
```

### Scan Multiple Ports
```
nmap -Pn -p 80,445,3389 [target IP address]
```

### Scan Range of Ports
```
nmap -Pn -p 1-65535 [target IP address]
```

### Fast Scan
```
nmap -Pn -F [target IP address]
```
- Will scan only the first 100 most common ports. 

### UDP Port Scan
```
nmap -Pn -sU [target IP address]
```

### UDP Top Port scan
```
nmap -sU --top-ports 25 --open [ip addr]
```
- This will only scan the top 25 ports, not the entirety. and only the open ports. Faster.

### Scan for Service Versions
```
nmap -Pn -sV [target IP address]
```

### Scan for Operating System
```
nmap -Pn -O [target IP address]
```

### Scan with All Defaults Scripts: 
```
nmap -Pn -sV -O -sC [target IP address]
```
- Runs the default (safe) Nmap scripts (`-sC` is equivalent to `--script=default`) on the open ports

### Aggressive Scan
```
nmap -Pn -A [target IP address]
```
- It combines the following into one switch: `-A`
- `-sV` : Version detection
- `-sC` : Script scanning
- `-O` : OS detection
- `–traceroute` : Traceroute

## Additional Options

### Increase speed
```
nmap -Pn -T5 -sV -O -sC [target IP address] -v
```
- Nmap's timing templates: T<0-5>: Set timing template (higher is faster)
- `-T0` : Paranoid
- `-T1` : Sneaky
- `-T2` : Polite	
- `-T3` : Normal
- `-T4` : Aggressive
- `-T5` : Insane

### Output to File
```
nmap [target IP address] -oN output.txt
```
- `-oN` : output normal
- `-oX` : output XML (allows you to import your output into a framework like MetaSploit).
- `-oS` : output ScriptKiddie
- `-oG` : output Grepable
- `-oA` : output All (all formats)
