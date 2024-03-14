# SSH

## Contents
- []()
- []()
- []()
- []()

## Syntax
```
ssh [user_name]@[host]  # host can be either IP or domain name
# OR
ssh -i [key-file] [user_name]@[host]
```

The default Key Name is `id_rsa`.
- The private key: `~/.ssh/id_rsa`
- The public key: `~/.ssh/id_rsa.pub`

**Note:** You need to chmod 600 id_rsa in order for the file to be usable in a session.

If you can obtain the `id_rsa` file of a target, just put it in your `~/.ssh` directory and ssh into the target machine. Or reference the key file with the `-i` switch if you put it in another directory somewhere.

## Copy via SSH

### Syntax
```
scp [source] [destination]
```

### Examples

To copy a file from B to A while logged into B:
```
scp /path/to/file username@a:/path/to/destination
#   [source]      [destination]           
```

To copy a file from B to A while logged into A:
```
scp username@b:/path/to/file /path/to/destination
#   [source]                 [destination]
```

## Tunneling
- start here
