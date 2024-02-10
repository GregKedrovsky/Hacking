# Scanning
> After host discovery, you check for open ports on the hosts you discovered. 

## Netcat: Manually Check Ports
- Nmap will find open ports, and a service version scan will most often provide detailed information about what services (and their versions) are running on those open ports.
- This is not, however, the case all the time.
- Whenever you see a question mark next to an nmap result, that means nmap is give you a best guess.
- **_You should always check these items manually with Netcat_**.
```
nc -nv [target ip] [port]
```


