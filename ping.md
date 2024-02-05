# ping

Ping Multiple IPs – One-Liners… ([Source](https://smallbusiness.chron.com/ping-ip-addresses-lan-68381.html))

## Windows

```
FOR /L %i IN (1,1,254) DO ping -n 1 192.168.0.%i | FIND /i "Reply"
```
- `FOR /L %i IN (1, 1, 254)` : Create a loop from 1 to 254, the range of valid IPs a 192.168.0.0/24 network.
- `DO ping -n 1 192.168.1.%i` : Follow the FOR loop by the ping command to execute on each iteration.
- `| FIND /i “Reply”` : filter to display only replies (kinda hinky, might need to tweak this a bit)

Redirect output to a file with: `> filename.txt`

## Linux:

```
for ip in $(seq 1 254); do ping -c 1 192.168.1.$ip; done
```
- `for ip in $(seq 1 254);` : create a loop from one to 254.
- `do ping -c 1 192.168.1.$ip; done` : ping the IP address, substituting the loop variable for the last part of the address, and then end the statement.

Redirect output to a file with: > `filename.txt`

