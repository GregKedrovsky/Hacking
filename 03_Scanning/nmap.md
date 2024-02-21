# NMap

## General Syntax & Steps: 

### Step 1 (option 1): Default Port Scan, Quick
```
nmap [target ip]
# or
nmap -Pn [target ip]
```

### Step 1 (option 2): Fast Scan, Quicker
```
nmap -Pn -F [target ip]
```
- `-F` : Scans 100 of the most commonly used ports (instead of the default 1000)

### Step 2: Scan Open Ports, Details
```
nmap -Pn -sS -sV -sC -O -T4 -p- [target ip]

# or an aggressive scan: 

nmap -Pn -A [target IP address]
```
- `-A` : combines the following into one switch
- `-sV` : Version detection
- `-sC` : Script scanning
- `-O` : OS detection
- `–traceroute` : Traceroute

## Additional: [nmap.md](/nmap.md)
