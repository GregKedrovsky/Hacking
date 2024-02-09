# Host Discovery
> In penetration testing host discovery is a crucial phase to identify live hosts on a network before further exploration and vulnerability assessment. Various techniques can be employed for host discovery, and the choice of technique depends on factors such as network characteristics, stealth requirements, and the goals of the penetration test.

## Content
- [Techniques](https://github.com/GregKedrovsky/Hacking/tree/main/02_Host_Discovery#techniques)
- [Choosing Techniques](https://github.com/GregKedrovsky/Hacking/tree/main/02_Host_Discovery#choosing-techniques)

## Techniques

### Ping sweeps:
- Sending an ICMP echo request (a ping) to a range of IP addresses to identify lives hoses. This is a quick and commonly used method. 

###  ARP Scanning:
- Using ARP (Address Resolution Protocol) requests to identify hosts on a local network. ARP scanning is effective in discovering hosts within the same broadcast domain. 

### TCP SYN Ping (Half-Open Scan): 
- Sending TCP SYN packets to a specific port (often port 80) to check if a host is alive.
- If the host is alive, it responds with a TCP SYN-ACK.
- This technique is stealthier than ICMP pings. 

### UDP Ping: 
- Sending UPD packets to a specific port to check if a host is alive.
- This can be effective for hosts that do not respond to ICMP or TCP probes.

### TCP ACK Ping: 
- Sending TCP ACK packets to a specific port to check if a host is alive.
- This technique expects no response, but if a TCP RST (reset) is received, it indicates that the host is alive. 

### SYN-ACK Ping (Sends SYN-ACK packets):
- Sending TCP SYN-ACK packets to a specific port to check if a host is alive.
- If a TCP RST is received, it indicates that the host is alive.

## Choosing Techniques

The choice of the "best" host discovery technique in penetration testing depends on various factors, and there is no "one size fits all" answer. 

The effectiveness of a host discovery technique can be influenced by the specific characteristics of the target network, the security controls in place, and the goals of the penetration test. 

Here are a few considerations: 
- ICMP Ping:
  - Pros: ICMP ping is a widely supported and quick method for identifying live hosts.
  - Cons: Solme hosts or firewalls may be configured to block ICMP traffic, thus limiting the effectiveness of a sweep. ICMP ping can also be easily detected.
- TCP SYN Ping:
  - Pros: TCP SYN ping is stealthier than ICMP and may bypass firewalls that allow outbound connections.
  - Cons: Some hosts may not respond to TCP S"YN requests, and the results can be affected by firewalls and security devices. 
