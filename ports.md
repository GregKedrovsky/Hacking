# Ports

|       Ports     | Quantity |        Description        |
| :-------------: |:-------: | --------------------------|
|   0 - 65,535    |  65,53   | Total ports               |
|     0 - 1023    |  1,024   | Well-Known (System) Ports |
|  1024 - 49,151  |  48,127  | Registered Ports          |
| 49,152 - 65,535 |  16,384  | Dynamic Ports             |

# Common Ports

|      Port     |  Protocol |             Service              |
| :-----------: | :-------: | ---------------------------------|
|  20           | TCP & UDP | FTP Data                         |
|  21           | TCP & UDP | FTP Control                      |
|  22           | TCP & UDP | SSH                              |
|  23           | TCP & UDP | Telnet                           |
|  25           | TCP & UDP | SMTP                             |
|  53           | UPD       | DNS                              |
|  67           | TCP & UDP | DHCP Server                      |
|  68           | TCP & UDP | DHCP Client                      |
|  69           | TCP & UDP | TFTP                             |
|  80           | TCP & UDP | HTTP                             |
|  88           | TCP & UDP | Kerberos                         |
|  110          | TCP & UDP | POP3                             |
|  111          | TCP & UDP | NFS (possibly)                   |
|  123          | TCP & UDP | NTP                              |
|  135          | TCP & UDP | MS-RPC EPMAP [1]                 |
|  136-139      | TCP & UDP | Net Bios                         |
|  137          | UDP       | NetBios Name Service             |
|  138          | UDP       | NetBios Datagram Service         |
|  139          | TCP       | NetBios Session Service, SMB [2] |
|  143          | TCP       | IMAP                             |
|  161          | UDP       | SNMP                             |
|  162          | TCP & UDP | SNMP Traps                       |
|  389          | TCP & UDP | LDAP [3]                         |
|  443          | TCP & UDP | HTTPS                            |
|  445          | TCP       | Microsoft AD & SMB [4]           |
|  500          | TCP & UDP | ISAKMP & IKE                     |
|  515          | TCP       | LDP                              |
|  1433         | TCP       | Microsoft SQL Server             |
|  1434         | TCP & UDP | Microsoft SQL Monitor            |
|  1521         | TCP       | Oracle Database Listener         |
|  1812 & 1813  | TCP & UDP | RADIUS                           |
|  2049         | TCP & UDP | NFS (possibly)                   |
|  3389         | TCP       | RDP (Windows)                    |
|  5355         | TCP & UDP | LLMNR [5]                        |

## Notes: 
1. Microsofts's Remote Procedure Call (RPC) Endpoint Mapper (EPMAP): An RPC is a communication process that allows for executing a subroutine or procedure in another address space.
2. SMB orignally ran on top of NetBios using port 139. NetBios is an older Transport Layer that allows Windows computers to talk to each other on the same network. SMB currently runs (mostly) on port 445 (TCP, over the Internet).
3. Lightweight Directory Access Protocol: Open, vendor-neutral standard application protocol for accessing and maintaining distributed directory information services over an IP network.
4. SMB used to run on port 139 (NetBios). Later versions of SMB (after Win2K) began to use port 445 on top of a TCP stack. Using TCP allows SMB to work over the Internet.
5. Link-Local Multicast Name Resolution: Protocol based on the DNS packet format. Allows both IPv4 and IPv6 hosts to perform name resolution for hosts on the same local link (LAN). Multicast IPv4 address: 224.0.0.252
