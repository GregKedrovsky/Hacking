# net Commands (Windows)

*And some other enumeration that goes along with net commands...*

The `net use` command (one of many `net` commands in Windows) is used to connect to, remove, and configure connections to shared resources, like mapped drives and network printers ([documentation](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/gg651155(v=ws.11))). 

If you need to list out the SMB shares in Windows: `net share`

**Syntax:**

```
net use /?                                               # will get you to the help 
net use * /delete                                        # Clear all stored sessions
net use /delete \\10.3.31.125\C$                         # Clear a specific stored session
net use z: \\10.3.31.125\C$                              # Mount a target on a specific drive letter
net use * \\10.3.31.125\C$                               # Mount a target on whatever letter
net use z: \\10.3.31.125\C$ [password] /user:[username]  # Mount with password and user name
```

Other `net` commands (and some other commands for Windows enumeration): 

```
whoami                         # enumerate the current user (hostname/username)
whoami /priv                   # enumerate the current user's current privileges
query user                     # enumerate the currently logged on users
net users:                     # enumerate all the other users on the system
net user [username]            # learn more about a particular user (shows password policy info)
net localgroup                 # enumerate the groups configured on the system
net localgroup administrators  # enumerate the users who are part of the Administrators group
```
