# [Nmap Host Discovery](https://nmap.org/book/man-host-discovery.html)

## Contents
- [List Scan](#list-scan)
- [Ping Sweep](#ping-sweep)
- [Scan for Open Ports](#scan-for-open-ports)
- [TCP SYN Ping](#tcp-syn-ping)

## List Scan
> A list scan doesn’t send traffic to any hosts but rather attempts a reverse DNS lookup to determine if a hostname can be found for the target. Occasionally, this will provide useful information.
- Syntax:
```
nmap -sL [target IP]
```

## Ping Sweep
> Nmap will not to do a port scan after host discovery, and only print out the available hosts that responded to the host discovery probes.
- Need root (sudo) privileges.
- Use your target subnet w/CIDR notation
```
nmap -sn 192.168.0.1/24
nmap -sn -v -T4 [target ip]/[CIDR]  # speed things up and get a bit more info
```
- `-sn` : ("scan no") Tells nmap to NOT perform a port scan because we don't want to scan the devices on the network right now, we want to FIND them.
- Hit `ENTER` while it's doing it's thing to see the status (percentage done).

## Scan for Open Ports
> A host discovery technieque that scans for open ports on the whole subnet. If a port is open, a machine/host is up.
- Syntax:
```
nmap --open -T4 [target ip subnet]/[CIDR]
nmap --open -T4 10.4.20.0/20
```
- `--open` : Only show hosts that have open ports, and only show the open ports for those.

## TCP ACK Ping
> This ping sweep host-discovery method is similar to the TCP SYN Ping, but instead of sending a SYN packet, here we send an ACK packet. 
- Nmap will send a TCP packet with the ACK flag set--it will sent that packet to target port 80 (or multiple IPs if you have specified them as such), or whatever target ports you specify.
- If the host is not active: nmap will receive nothing back.
- If the host is active: nmap will receive a TCP packet with the RST (reset) flag set.

### Syntax:
```
nmap -sn -PA [target ip or range or file]
nmap -sn -PA3389 [target ip]
nmap -sn -PA1-1000 [target ip] 
```
- `-sn` : Ping Scan - disable port scan
- `-PA[port list]`: TCP ACK Ping
  - Port 80 is default, so you don't need to specify the port unless you want something different.
  - Note that there can be **_no space_** between -PA and the port list (if one is provided).

### Common Problem with this Method
- Often systems will be configured to block incoming ACK packets (b/c servers should be receiving SYN packets first, etc.).
- If you know a host is online (e.g., it responds to a TCP SYN Ping) but your TCP ACK Ping fails (shows no hosts active/online), then you know there is a firewall configured on the system (likely Windows firewall) because it is blocking the ACK packets being sent.

## TCP SYN Ping
> Use a SYN ping for common ports only.

### Standard Syntax:
```
nmap -sn -PS21,22,25,80,445,3389,8080 -T4 [target ip]/[CIDR]
```
- `-PS[port list]`: TCP SYN Ping. The port list allows you to add the most common ports for Linux and Windows to save time.
  - Note that there can be **_no space_** between `-PS` and the port list.

### UDP Syntax:
```
nmap -sn -PS21,22,25,80,445,3389,8080 -PU137,138 -T4 [target ip]/[CIDR]
```
- `-PU [port list]` : Add to run a UDP ping scan on selected known/common UDP ports.
- Upon hitting a closed port on the target machine, the UDP probe should elicit an ICMP port unreachable packet in return. This signifies to Nmap that the machine is up and available.
  - Note that there can be **_no space_** between `-PU` and the port list.

## ICMP Specific Ping Scan
> This is a host discovery scan than only sends ICMP Echo Requests (just like when we used the ping command). 
- Syntax:
```
nmap -sn -PE [target ip]
```
- `-sn` :  Ping Scan - disable port scan
- `-PE` :  Nmap sends an ICMP type 8 (echo request) packet to the target IP addresses, expecting a type 0 (echo reply) in return from available hosts.
- Problem: Most firewalls today block these kinds of requests. Could try a modified version: 
```
nmap -sn -PE [target ip] --send-ip
```
- `--send-ip:` send packets via raw IP sockets rather than sending lower level ethernet frames
