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
- Option: `-sn` : ("scan no") Tells nmap to NOT perform a port scan because we don't want to scan the devices on the network right now, we want to FIND them.
- Use your target subnet w/CIDR notation
- Hit `ENTER` while it's doing it's thing to see the status (percentage done).
```
nmap -sn 192.168.0.1/24
nmap -sn -v -T4 [target ip]/[CIDR]  # speed things up and get a bit more info
```

## Scan for Open Ports
> A host discovery technieque that scans for open ports on the whole subnet. If a port is open, a machine/host is up.
- Option: `--open` : Only show hosts that have open ports, and only show the open ports for those.
- Syntax:
```
nmap --open -T4 [target ip subnet]/[CIDR]
nmap --open -T4 10.4.20.0/20
```

## TCP SYN Ping
> Use a SYN ping for common ports only.
- Option: `-PS [port list]`: TCP SYN Ping. The port list allows you to add the most common ports for Linux and Windows to save time.
- Syntax:
```
nmap -sn -PS 21,22,25,80,445,3389,8080 -T4 [target ip]/[CIDR]
```
- Add: `-PU [port list]` to run a UDP ping scan on selected known/common UDP ports.
- Upon hitting a closed port on the target machine, the UDP probe should elicit an ICMP port unreachable packet in return. This signifies to Nmap that the machine is up and available.
```
nmap -sn -PS 21,22,25,80,445,3389,8080 -PU 137,138 -T4 [target ip]/[CIDR]
```












