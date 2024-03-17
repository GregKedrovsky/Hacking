# Redeemer

TARGET MACHINE IP ADDRESS
10.129.64.170

## Nmap

### This one took a LONG time... 
```
nmap -Pn -sS -p- 10.129.64.170
```

### This one was quicker...
- The questions revolved around "redis," so I just searched on what port redis used... 
```
nmap -sS -sV -sC -O -p 6379 10.129.64.170
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-20 20:25 EST
Nmap scan report for 10.129.64.170
Host is up (0.049s latency).

PORT     STATE SERVICE VERSION
6379/tcp open  redis   Redis key-value store 5.0.7
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.8 (96%), Linux 2.6.32 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), Linux 5.3 - 5.4 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 - 5.4 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.65 seconds

```

## redis-cli
```
redis-cli -h 10.129.64.170
# connect to the redis db

10.129.64.170:6379> help [tab]
# tab through help topics

10.129.64.170:6379> INFO
# Display information and statistics

10.129.64.170:6379> select 0
# select redis local db... new connections always use the database 0.

10.129.64.170:6379> KEYS *
1) "numb"
2) "flag"
3) "temp"
4) "stor"
# keys are the fundamental data structure used to identify, store, and retrieve data.
# each key in Redis maps to a corresponding value

10.129.64.170:6379> get flag
"03e1d2b376c37ab3f5319922053953eb"
# GET retrieves the value associated with the specified key
```

"Key" Source: https://www.geeksforgeeks.org/a-complete-guide-to-redis-keys/
