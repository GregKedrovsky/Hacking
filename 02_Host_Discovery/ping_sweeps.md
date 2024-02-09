# Ping Sweeps

> A ping sweep is a network scanning technique used to discover live hosts (computers, servers, or other devices) within a specific IP address range on a network. The basic idea is to send a series of ICMP Echo Request (ping) messages to a range of IP addresses and observe the responses to determine which addresses are active or reachable.

NOTE: Windows blocks ICMP traffic by default.

## Contents
- [ping](#ping)
  - [Windows One-Liner](#windows-one-liner)
  - [Linux One-Liner](#linux-one-liner)
- [fping](#fping)
- [Bash Ping Sweep](#bash-ping-sweep)

## ping

### Basic Syntax:
```
ping -b -c 1 10.10.23.0
```
- `-b` : allow pinging a broadcast address (which will send your ping out to the entire network; and then you'll see which hosts reply)
- `-c` *count* : how many pings to send out

### [Windows One-Liner](https://smallbusiness.chron.com/ping-ip-addresses-lan-68381.html):
```
FOR /L %i IN (1,1,254) DO ping -n 1 192.168.0.%i | FIND /i "Reply"
```

How it works:
- `FOR /L %i IN (1, 1, 254)` : Create a loop from 1 to 254, the range of valid IPs a 192.168.0.0/24 network.
- `DO ping -n 1 192.168.1.%i` : Follow the FOR loop by the ping command to execute on each iteration.
- `| FIND /i “Reply”` : filter to display only replies (kinda hinky, might need to tweak this a bit)

Redirect output to a file with: `> filename.txt`

### [Linux One-Liner](https://smallbusiness.chron.com/ping-ip-addresses-lan-68381.html):
```
for ip in $(seq 1 254); do ping -c 1 192.168.1.$ip; done
```

How it works:
- `for ip in $(seq 1 254);` : create a loop from one to 254.
- `do ping -c 1 192.168.1.$ip; done` : ping the IP address, substituting the loop variable for the last part of the address, and then end the statement.

Redirect output to a file with: `> filename.txt`

## fping
> Supposedly an improvement of ping... (simplified and more efficient)

### Syntax
```
fping: fping -a -g 10.10.23.0/24 2>/dev/null
```
- `-a`  : alive; show systems that are alive
- `-g` : generate a target list from a supplied IP netmask, or a starting and ending IP.
- `2>/dev/null` : send stderr (fd2) to /dev/null (rather than displaying it on the screen)

## Bash Ping Sweep

One-liner: All failures go to /dev/null and all successful pings are registered on stdout.
```
for ip in 192.168.56.{101..110}; do ping -c 1 $ip > /dev/null && echo "${ip} is up"; done
```

Simple sweep of a network for a quick look at what machines are out there (and respond):
```
for i in {1..254}
do
    ping -w 5 -c 1 192.168.0.$i | grep "64 bytes" | cut -d " " -f 4 | tr -d ":" &
done

wait  # with no args: wait until all background processes to finish
```
Note the `&` at the end. This will speed things up a lot.
