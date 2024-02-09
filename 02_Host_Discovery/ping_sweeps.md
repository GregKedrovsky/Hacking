# Ping Sweeps

> A ping sweep is a network scanning technique used to discover live hosts (computers, servers, or other devices) within a specific IP address range on a network. The basic idea is to send a series of ICMP Echo Request (ping) messages to a range of IP addresses and observe the responses to determine which addresses are active or reachable.

## Contents
- ping
- fping
- Bash Ping Sweep

## ping


## fping


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

wait # no args, wait until all background processes to finish

Note the “&“ at the end. This will speed things up a lot.
```
