# SSH

## Contents
- [Syntax](#syntax)
  - [Password](#password)
  - [Key File](#key-file)
- [Copy via SSH](#copy-via-ssh)
  - [Syntax](#syntax-1)
  - [Examples](#examples)
- [Tunneling](#tunneling)

## Syntax

### Password
```
ssh [user_name]@[host]  # host can be either IP or domain name
# If you are not on default port 22 you have to add the -p switch:
ssh - [port] [user_name]@[host]
```

### Key File
```
ssh -i [key-file] [user_name]@[host]
```

The default Key Name is `id_rsa`.
- The private key: `~/.ssh/id_rsa`
- The public key: `~/.ssh/id_rsa.pub`

If you can obtain the `id_rsa` file of a target, just put it in your `~/.ssh` directory and ssh into the target machine. 
- Or reference the key file with the `-i` switch if you put it in another directory somewhere.
- **Note:** You need to chmod 600 id_rsa in order for the file to be usable in a session.

## Copy via SSH

### Syntax
```
scp [source] [destination]
```

### Examples

To copy a file from local (attack) machine to a target:
```
scp /path/to/file username@[target ip]:/path/to/destination
#   [source]      [destination]           
```

To copy a file from the target B to your local (attack) machine
```
scp username@[target ip]:/path/to/file /path/to/destination
#   [source]                 [destination]
```

## Tunneling
- By using tunneling, you can create a secure connection between your local machine and the internal network, allowing you to access the internal services as if you were on the network itself.
- This can be particularly useful for remote employees who need to access internal resources on a remote system but do not have direct access to the company's network. 

###  Local port forwarding
> When local port forwarding is used, a separate tunnel is created inside the existing valid SSH session that forwards network traffic from a local port on the client's machine over to the remote server's port. Under the hood, SSH allocates a socket listener on the client on the given port. When a connection is made to this port, the connection is forwarded over the existing SSH session over to the remote server's port.

### Remote port forwarding
> Also called `Reverse Tunneling`, this is exactly the opposite operation of a local port forwarding tunnel . Again, after a successful SSH connection, a separate tunnel is created which SSH uses to redirect incoming traffic to the server's port back to the client. Internally, SSH allocates a socket listener on the server on the given port. When a connection is made to this port, the connection is forwarded over the existing SSH session over to the local client's port.

### Dynamic port forwarding
> The main issue with both local and remote forwarding is that a local and a remote port have to be defined prior to the creation of the tunnel. To address this issue, one can use dynamic tunneling . Dynamic tunneling, allows the user to specify just one port that will forward the incoming traffic from the client to the server dynamically.

### Example: PostgreSQL
> Local port forwarding to access a remote PostgreSQL server (on the target). From the HTB [Funnel](Hack-the-Box/00_Starting_Point/16_funnel.md) box.

#### PostgreSQL
- TCP port is open 5432 (do a better nmap scan!! The question to answer on HTB led me to this port; I didn't find it)
- That port is used by PostgreSQL and it only listens only on localhost so if you want to connect remotely, you have to use ssh...
- Since you can't access PostgreSQL from the local machine, you will have to create a tunnel with local port forwarding
- Ref: https://www.postgresql.org/docs/current/ssh-tunnels.html

#### SSH Tunneling to PostgreSQL
- Following the reference link above, I did: 
```
ssh -L 63333:localhost:5432 christine@10.129.34.254
psql -h localhost -p 63333 --username=christine postgres

# Result: postgres prompt...

postgres=# 
postgres-# \?     # for help
postgres-# \l     # list all databases
                                                        List of databases                                                                                   
   Name    |   Owner   | Encoding | Locale Provider |  Collate   |   Ctype    | ICU Locale | ICU Rules |    Access privileges                               
-----------+-----------+----------+-----------------+------------+------------+------------+-----------+-------------------------                           
 christine | christine | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           |                                                    
 postgres  | christine | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           |                                                    
 secrets   | christine | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           |                                                    
 template0 | christine | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           | =c/christine           +                           
           |           |          |                 |            |            |            |           | christine=CTc/christine                            
 template1 | christine | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           | =c/christine           +                           
           |           |          |                 |            |            |            |           | christine=CTc/christine                            
(5 rows)  

postgres-# \c secrets      # connect to database
psql (16.2 (Debian 16.2-1), server 15.1 (Debian 15.1-1.pgdg110+1))                                                                                          
You are now connected to database "secrets" as user "christine". 

secrets-# \dt               # list all database tables
 public | flag | table | christine

secrets=# SELECT * FROM flag;
 cf277664b1771217d7006acdea006db1

```

