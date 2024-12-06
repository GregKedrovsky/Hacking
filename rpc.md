# Remote Procedure Call (RPC)
>An RPC is a communication process that allows for executing a subroutine or procedure in another address space.

|    Port    |  Protocol |    Service   |
| :--------: | :-------: | -------------|
|  135       | TCP & UDP | MS-RPC EPMAP |

Microsofts's Remote Procedure Call (MS-RPC) Endpoint Mapper (EPMAP).

## rpcclient
>Tool for executing client side MS-RPC functions

### General Syntax:
```
rpcclient -U "" -N [target ip addr]
```
- This tries to connect with (-U) a null username (none) and (-N) no password.
- If you connect, type `?` to see a help screen.

### After connecting:

```
> serverinfo
> lsaenumsid
> netshareenumall
```
