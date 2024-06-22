# MetaSploit

Resources: [MetaSploit Docs](https://docs.metasploit.com/) | [MetaSploit Unleased by OffSec](https://www.offsec.com/metasploit-unleashed/)

Start MetaSploit: `msfconsole`

Help Menu in MetaSploit: `search -h` ([additional](https://www.offsec.com/metasploit-unleashed/msfconsole-commands/#search)]

----

## Contents

---- 

## Workspaces
- MSFconsole provides you with the ability to create, manage, and switch between multiple workspaces depending on your requirements. 
- Create a new workspace for each engagement (so you don't just dump everything always into the default workspace).
- Workspaces allow you to keep track of all your hosts, scans, and activities. They are extremely useful when conducting pentests as they allow you to sort and organize your data based on the target or organization. 

### PostreSQL

All of the information for your workspaces (including the content you generate) is stored in the MSFdb.
- Which is why you need to make sure PostgreSQL is started and running, and your MSFdb is initialized.

**First:** Check the status of your MSFdb.
```
> db_status
```

If your database is not connected, do the following. 

**Start and enable the PostgreSQL db service:** At the Linux (not msf) prompt as root... 
```
systemctl enable postgresql  
systemctl start postgresql  
systemctl status postgresql  # or  service postgresql status
```

You may need to initialize the MSFdb: At the Linux (not msf) prompt as root...
```
msfdb        # this gives you the help menu
msfdb init   # this will initialize or re-initialize your msfdb
msfdb status # this will check the states of your msfdb
```

Start `msfconsole` and check to make sure you can connect (in msfconsole, type `db_` and TAB to see available commands)
```
db_status
```

**Second:** Check out the help menu if you need it.
```
> workspace -h
```

What workspace are you currently using? 
```
> workspace
```
- This will show you the name of the workspace you are using (e.g., default).
- The asterisk * indicates the currently selected workspace.

Check the data in the workspace (see below for more commands): 
```
> hosts
> services
> loot
> creds
> vulns
```

Create a new workspace: 
```
> workspace -a [name]
> workspace            # should show you the new workspace in the list
```

Switch back to the default workspace from your newly created workspace: 
```
> workspace default
```

Delete a workspace: 
```
> workspace -d [name to delete]
```

Rename a workspace: 
```
> workspace -r [current name of workspace] [new name of workspace]
```

## Search for Modules

```
search [name or portion of name]
```

You can narrow your search using keywords (`search -h` for a list). Example:
```
search type:auxiliary name:ftp
```

## Select Modules

```
use [name or number]
```

## Configure Module Options & Variables

```
options
# or
show options
set [option mame] [value]
```

## Payload

A [payload](https://www.offsec.com/metasploit-unleashed/payloads/) in Metasploit refers to an exploit module. There are three different types of payload modules in the Metasploit Framework: Singles, Stagers, and Stages. 

## Sessions


## Additional Functionality


## Save Your Configuration
