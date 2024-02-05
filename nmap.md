# Nmap

## Resources
- [Reference Guide](https://nmap.org/book/man.html)
- [Host Discovery](https://nmap.org/book/man-host-discovery.html)
- [NSE Script List](https://nmap.org/nsedoc/scripts/)

Find scripts on your system:
```
locate *.nse
# or in Kali
ls -l /usr/share/nmap/scripts | grep ftp-*  # (or smb, http, etc.)
```
**Combine Options:** `nmap -sU -sV -sC [target IP]` is the same as `nmap -sUVC [target IP]`

**Verbosity:** Increase the verbosity of the output with `-v`, and more with `-vv`

## Host Discovery

Standard:
```
nmap -sn 192.168.0.1/24
```
- The `-sn` tells nmap to **_noit_** perform a port scan ("scan no" option: `-sn`) because we don't want to scan the devices on the network right now, we want to FIND them. 
- This is often known as a "ping scan" or a "ping sweep."  
- Hit `ENTER` while it's doing it's thing to see the status (percentage done).

Variant: Scan for open ports on the whole subnet... 
```
nmap --open -T4 [target ip subnet]/[CIDR]
nmap --open -T4 10.4.20.0/20
```
- `--open` : Only show hosts that have open ports, and only show the open ports for those.

## Port Scans

Default scan, Ping no: 
```
nmap -Pn [target IP or CIDR notation network]
```
- This disables host discovery. 
- It causes Nmap to attempt the requested scanning functions against every target IP address specified. 
- Nmap performs the requested functions as if each specified target IP is active.

Scan all ports on a machine:
```
nmap -Pn -p- [target IP address]
```

Scan for open ports:
```
nmap [target IP or IP/CIDR subnet] --open
```
- `--open`: Only show hosts that have open ports, and only show the open ports for those

Scan one specific port: 
```
nmap -Pn -p 80 [target IP address]
```

Scan multiple ports: 
```
nmap -Pn -p 80,445,3389 [target IP address]
```

Scan a range of ports: 
```
nmap -Pn -p 1-65535 [target IP address]
```

Fast Scan: 
```
nmap -Pn -F [target IP address]
```
- Will scan only the first 100 most common ports. 

UDP Port Scan: 
```
nmap -Pn -sU [target IP address]
```

UDP Top Port scan: 
```
nmap -sU --top-ports 25 --open [ip addr]
```
- This will only scan the top 25 ports, not the entirety. and only the open ports. Faster.

Scan for service versions : 
```
nmap -Pn -sV [target IP address]
```

Scan for operating system: 
```
nmap -Pn -O [target IP address]
```

Scan with all defaults scripts: 
```
nmap -Pn -sV -O -sC [target IP address]
```
- Runs the default (safe) Nmap scripts (`-sC` is equivalent to `--script=default`) on the open ports

Aggressive Scan: 
```
nmap -Pn -A [target IP address]
```
- It combines the following into one switch: `-A`
- `-sV` : Version detection
- `-sC` : Script scanning
- `-O` : OS detection
- `–traceroute` : Traceroute

Increase speed: 
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

Output to File: 
```
nmap [target IP address] -oN output.txt
```
- `-oN` : output normal
- `-oX` : output XML (allows you to import your output into a framework like MetaSploit).
- `-oS` : output ScriptKiddie
- `-oG` : output Grepable
- `-oA` : output All (all formats)
