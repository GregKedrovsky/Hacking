# [Masscan](https://github.com/robertdavidgraham/masscan)
> This is an Internet-scale port scanner. It can scan the entire Internet in under 5 minutes, transmitting 10 million packets per second, from a single machine.

## Syntax:
```
masscan -p1-65535 192.168.0.229
```

## Speed it up with the rate option:
```
masscan -p1-65535 --rate 1000 192.168.0.229
# Similar to: nmap -T4 -p- 192.168.0.229
```

## Extended Syntax (for HTB):
```
masscan 10.10.10.4 -p1-65535,U:1-65535 --rate=1000 -e tun0
# -p1-65535,U:1-65535 # scan all TCP/UDP ports
# --rate=1000         # scan rate = 1000 packets per second
```
