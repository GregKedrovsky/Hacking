# NMap

## Contents
- [General Syntax](#general-syntax)
- [Standard Go-To](#standard-go-to)
- [Default Port Scan](#default-port-scan)
- [Fast Scan](#fast-scan)
- [Specify Ports to Scan](#specify-ports-to-scan)
- [TCP Connect Scan](#tcp-connect-scan)
- [UDP Port Scan](#udp-port-scan)
- [Version Scan](#version-scan)
- [Operating System Scan](#operating-system-scan)
- [Default Script Scan](#default-script-scan)
- [Aggressive Scan](#aggressive-scan)
- [Timing Templates](#timing-templates)
- [Output to File](#output-to-file)

## General Syntax
```
nmap [Scan Type(s)] [Options] {target specification}
# need to run as root
```
- If you are dealing with a Windows system and you get a "**_filtered_**" STATE in your response, you can be assured the target system has a **_firewall_**.
- If you are dealing with a Windows system and you get a "**_closed_**" STATE in your response, then Windows Firewall is **_not active_** or there are **_not rules_** for particular port that are intercepting traffic.
- `-v` provides some verbosity. `-vv` provides more verbosity.

## Standard Go-To
```
#TCP Ports:
nmap -sS -T4 -p- -A -vv [IP Address]
nmap -sS -T4 -p- -A -vv --reason [IP Address]

#UDP Ports: 
nmap -sU -T4 -A -vv [IP Address]
nmap -sU -T4 -A -vv --reason [IP Address]
```

## Default Port Scan
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

## Fast Scan
```
nmap -Pn -F [target ip]
```
- `-F` : Scans 100 of the most commonly used ports (instead of the default 1000)

## Specify Ports to Scan
```
# Single Port:
nmap -Pn -p 80 [target ip]
# Series of Ports:
nmap -Pn -p 80,22,25,445,3389,8080 [target ip]
# Range of Ports:
nmap -Pn -p 1-100 [target ip]
```

## TCP Connect Scan
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

## UDP Port Scan
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

## Version Scan
```
nmap -sV [target IP address]
```
- `-sV` : show the version of the service running on the open port.

## Operating System Scan
```
nmap -O [target IP address]
```
- `-O` : This will attempt to ID the O/S running on the target system

## Default Script Scan
```
nmap -sC [target IP address]
```
- `-sC` : Runs the default (safe) Nmap scripts on the open ports (equivalent: `--script=default`).

## Aggressive Scan
```
nmap -A [target IP address]
# same as:
nmap -sV -O -sC [target IP address]
```
- `-A` : Combines the following:
  - `-sV` : Version Scan
  - `-O` : Operating System Scan
  - `-sC` : Default Script Scan

## Timing & Performance

### Templates
> Speed up a scan: Use  Nmap's timing templates... (higher is faster)
- `T0` : Paranoid
- `T1` : Sneaky
- `T2` : Polite
- `T3` : Normal
- `T4` : Aggressive
- `T5` : Insane
```
nmap -Pn -sV -O -sC -T5 [target IP address] -v
```

### Additional Options
```
nmap -sS -sV -F --scan-delay 5s [target ip]
```
- `--scan-delay time` or `--max-scan-delay time` :  Adjust delay between probes.
- You can append `ms` (milliseconds), `s` (seconds), `m` (minutes), or `h` (, or hours). 

You can also add `--host-timeout [time]` to give up on slow target hosts.
- The time parameter is specified in seconds by default.
- You can append `ms` (milliseconds), `s` (seconds), `m` (minutes), or `h` (, or hours).
- TIP: Set it to `30s` if you are working on a large network and/or with a large group of IPs.

## Output to File
> Output your scan results to a file so you don't have to go back and do it again.
- `-oN` : output normal
- `-oX` : output XML (allows you to import your output into a framework like MetaSploit).
- `-oS` : output ScriptKiddie
- `-oG` : output Grepable
- `-oA` : output All (all formats)
```
nmap -Pn -sV -O -sC -T5 [target IP address] -v -oN output.txt
```
