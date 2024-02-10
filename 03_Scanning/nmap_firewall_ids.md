# Nmap: Firewall Detection & IDS Evasion

## Contents

## Firewall Detection

**How to detect the presense of a firewall when port scanning:** 
- Nmap: `-sA` (TCP ACK scan)
- Used to map out firewall rulesets, determining whether they are stateful or not and which ports are filtered.

**Unfiltered / Filtered:** 
- When scanning unfiltered systems, open and closed ports will both **_return_** an `RST` packet. Nmap then labels them as **_unfiltered_** (reachable).
- Ports that **_don't respond_**, or send certain ICMP error messages back (type 3, code 1, 2, 3, 9, 10, or 13), are labeled **_filtered_**. 

**First, host discovery:** 
```
nmap -sn [target ip]
```
**Second, quickie to see what's out there:**
```
nmap -Pn -sS -F [target ip]     # -F : Scan only the 100 most commonly used ports
```

Confirm the suspision that there is no firewall by using the `-sA` (ACK scan): 

## IDS Evasion
> This is a much more advanced topic and is only introduced here.

`nmap -h` : You'll see there is an entire section dedicated to FIREWALL/IDS EVASION AND SPOOFING

### Fragmented Packets
> One method to avoid detection is to use fragmented packets. 
- This way the IDS cannot tell what's in the packets because they're not whole.
- The easiest way to do this is to just add the -f (lower case) option.

```
nmap -Pn -sS -sV -p 80,445,3389 [target ip]      # normal scan
nmap -Pn -sS -sV -p 80,445,3389 -f [target ip]   # fragmented
```
- This will fragment the packets--break them up.
- They are reassembled on the other side by use of an "offset" value.

### Fragment + MTU
> MTU =  Minimum Transmission Unit (size)

You can add the `-mtu` option and provide a custom value: 
```
nmap -Pn -sS -sV -p 80,445,3389 -f -mtu 32 [target ip]
# set at mtu 32, there will likely be no fragmentation of packets. So...
nmap -Pn -sS -sV -p 80,445,3389 -f -mtu 8 [target ip]
```
