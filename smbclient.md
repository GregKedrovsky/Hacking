# smbclient

`smbclient` is part of the Linux samba suite.
- It is a client that can 'talk' to an SMB/CIFS server.
- It offers an interface similar to that of the ftp program.
- Operations include things like getting files from the server to the local machine, putting files from the local machine to the server, retrieving directory information from the server and so on. 
- [Reference](https://github.com/irgoncalves/smbclient_cheatsheet)

## Syntax Note: Slashes

**Slashes: Try until you find one that works.**
- For examples, if you are trying to reach a directory that has been shared as 'public' on a machine called zimmerman, the service would be called `\\zimmerman\public`.
- However, due to shell restrictions, you will need to escape the backslashes, so you end up with something like this: `smbclient \\\\zimmerman\\public mypasswd`

RFC: ([rfc3986](https://tools.ietf.org/html/rfc3986))
- File URIs can have two or three slashes; both are syntactically correct but semantically different.
- Three slashes (equivalent to omitting “localhost” between the second and third slash) is used to access local files.
- Two slashes (as long as the host is not “localhost”) is used to access files in a remote system.

## Usage & Examples

List shares on a machine using NULL Session:
```
smbclient -L
```

List shares on a machine using a valid username + password:
```
smbclient -L <target-IP> -U username%password
```

Connect to a valid share with username + password:
```
smbclient //<target>/<share$> -U username%password
```

List files on a specific share:
```
smbclient //<target>/<share$> -c 'ls' password -U username
```

List files on a specific share folder inside the share:
```
smbclient //<target>/<share$> -c 'cd folder; ls' password -U username
```

Download a file from a specific share folder:
```
smbclient //<target>/<share$> -c 'cd folder;get desired_file_name' password -U username
```

Copy a file to a specific share folder:
```
smbclient //<target>/<share$> -c 'put /var/www/my_local_file.txt .\target_folder\target_file.txt' password -U username
```

Create a folder in a specific share folder:
```
smbclient //<target>/<share$> -c 'mkdir .\target_folder\new_folder' password -U username
```

Rename a file in a specific share folder:
```
smbclient //<target>/<share$> -c 'rename current_file.txt new_file.txt' password -U username
```
