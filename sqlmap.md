# SQLMap
> SQLmap is an open-source tool used in penetration testing to detect and exploit SQL injection flaws. SQLmap automates the process of detecting and exploiting SQL injection. SQL Injection attacks can take control of databases that utilize SQL.

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

## Obtain Cookie & Parameters

You will need to provide a cookie to the sqlmap in order for it to find vulnerability. The reason why we have to provide a cookie is because of authentication.
- To grab the cookie, we can intercept any request in Burp Suite & get it from there.
- Or you can install an extension for your web browser called `cookie-editor`: [chrome](https://chrome.google.com/webstore/detail/cookie-editor/hlkenndednhfkekhgcdicdfddnkalmdm) or [firefox](https://addons.mozilla.org/en-US/firefox/addon/cookie-editor/)
- The cookies in HTTP messages of requests are usually set the following way: `PHPSESSID=7u6p9qbhb44c5c1rsefp4ro8u1`

1. Open up Burp Suite, go to Proxy, turn on Intercept
2. Open Firefox browser and set proxy to Burp (FoxyProxy)
3. Open target website (web page)
4. In Burp you should have intercepted the request.
5. In that request should be a line "Cookie: ..."
6. This is also how you would grab the parameters you want to test (e.g., username, password, id, etc.)

## General Syntax & Example

```
sqlmap -u "http://[target ip]/[php line from Burp GET request]" --cookie "[PHPSESSIONID...security_level=0" -p title
```
- `- u`: Target URL (e.g. "http://www.site.com/vuln.php?id=1")  
- `--cookie`: HTTP Cookie header value 
- `-p`: Testable parameter(s) [we took "title" from the PHP value in the URL; that's what we want to test]

Run that. It takes a while. Results should give you some things to try out. 

The "Payload:" items are what you can copy and paste into Burp's Repeater.

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

## POST Login Pages

Post login pages are authorized by the cookie header, which is passed in the HTTP header of a GET/POST request. To scan the post login page(s), we have to provide the valid cookie to SQLMap.
- Example: (note: `/admin/index.php?id=1` is a POST login page)
```
sqlmap -u http://192.168.202.163/admin/index.php?id=1 --cookie="cookie value"
```

## Crawl

Crawl is an important option which allows the SQLMap tool to crawl the website, starting from the root location. The depth to crawl can be defined in the command.

```
sqlmap -u http://192.168.202.160/ --crawl=1
```
