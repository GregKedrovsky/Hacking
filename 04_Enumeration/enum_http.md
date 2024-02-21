# Enumeration: HTTP

## Contents
- [Concepts & Methodology](#concepts--methodology)
  - [HTTP](#http)
  - [IIS](#iis)
  - [WebDAV](#webdav)
  - [Initial Recon](#initial-recon)
- [Linux CLI](#linux-cli)
  - [curl](#curl)
    - [Usage & Syntax](#usage--syntax)
    - [General Enumeration](#general-enumeration)
    - [GET Request](#get-request)
    - [HEAD Request](#head-request)
    - [Method (Options) Inquiry](#method-options-inquiry)
    - [Options are Not Site-Wide](#options-are-not-site-wide)
  - [wget](#wget)
- [Directory Busting](#directory-busting)
  - [DirB](#dirb)
  - [GoBuster](#gobuster)
    - [General Syntax](#general-syntax)
    - [Modes/Commands](#modescommands)
    - [DIR Mode/Command](#dir-modecommand)
    - [DIR Mode Flags/Options](#dir-mode-flagsoptions)
    - [Syntax 1: Standard DirBust](#syntax-1-standard-dirbust)
    - [Syntax 2: Blacklist Codes](#syntax-2-blacklist-codes)
    - [Syntax 3: Find Files](#syntax-3-find-files)
    - [Syntax 4: Specific Directory](#syntax-4-specific-directory)
- [Nmap Scripts](#nmap-scripts)
  - [Default Scripts](#default-scripts)
  - [banner](#banner)
  - [http-enum](#http-enum)
  - [http-headers](#http-headers)
  - [http-methods](#http-methods)
  - [http-webdav-scan](#http-webdav-scan)
- [MetaSploit](#metasploit)
  - [brute_dirs](#brute_dirs)
  - [http_version](#http_version)
- [robots.txt](#robotstxt)

## Concepts & Methodology

### HTTP
- HyperText Transfer Protocol is used for hosting websites, both internal and external
- Everybody has at least one site and there are SO many different programs and languages out there used to build websites that they can't secure them all.
- Broad attack surface
- NOTE: Websites are basically just APIs.

### IIS
- Internet Information Services (IIS) is the Windows HTTP Server.
- A fully-functional web server for Windows environments

### WebDAV
- WebDAV (Web Distributed Authoring and Versioning) is a mechanism that provides for collaborative editing of remote content. A webserver that supports WebDAV simultaneously works like a fileserver.
- WebDAV extends the set of standard HTTP methods and headers to provide the ability to create a file or folder, edit a file in place, copy or move or delete a file, etc. (much like FTP).
- WebDAV often provide an excellent attack vector.

### Initial Recon

#### Scan Like Normal
- When you get your target IP... run an nmap default scan against it to see what you have...
- Port 80 is the default for HTTP
- You may also see ancillary ports open (SMB, SQL, etc.)
- Scan for service version info and O/S info: `-sV` and `-O`

#### Browse the Target
- When you see you have a port 80 open, plug your target IP into a browser and see what comes up.

## Linux CLI

### curl
> curl (short for "Client URL") is a command line tool that enables data transfer over various network protocols. It communicates with a web or application server by specifying a relevant URL and the data that need to be sent or received.

#### Usage & Syntax
```
curl [options...] <url>
```
- `-I, --head` : Show document info only
- `-X, --request <command>` : Specify request command to use
- `-v, --verbose` : Make the operation more talkative
- `url` : The URL syntax is protocol-dependent. If you provide a URL without a leading `protocol://` scheme, curl guesses what protocol you want. It then defaults to HTTP.

#### General Enumeration
```
curl [target ip] | more
```
- Enumerate any useful information that curl retrieves for you 

#### GET Request
- Send a GET request to the server using curl:
```
curl -X GET [target ip]  
```
- We should get the index/home page.

#### HEAD Request
- Send a HEAD request to the server using curl: 
```
curl -I HEAD [target ip]
```

#### Method (Options) Inquiry
- Find out what methods are allowed on the server:
```
curl -X OPTIONS [target ip] -v
```
- The verbose (`-v`) option is necessary
- Result will look like: "Allow: GET, HEAD, OPTIONS"
- So if you try `curl -X PUT [target ip]` you will get a response of "Method Not Allowed"

#### Options are Not Site-Wide
```
curl -X OPTIONS [target ip]/login.php -v
```
- This could show "Allow: GET, POST, HEAD, OPTIONS"
- This page allows POST because it uses POST for authentication.
- So, test that out:
```
curl -X POST [target ip]/login.php -v
```
- Continue with (for example):
```
curl -X POST [target ip]/login.php -d "name=john&password=password" -v
```

### wget
> wget retrieves files you specify by url.
```
http://[target ip]/index
```
- Then you just cat the file to see what info is provided. Enumerate.

## Directory Busting

### [DirB](https://www.kali.org/tools/dirb/)
> DIRB is a Web Content Scanner. It looks for existing (and/or hidden) Web Objects. It basically works by launching a dictionary based attack against a web server and analyzing the responses.
```
dirb http://[target ip]
# Or you can pass dirb a custom wordlist:
dirb http://[target ip] /usr/share/metasploit-framework/data/wordlists/directory.txt
```
- NOTE: dirb needs a URL not just an IP address
- DirB sends GET requests to predetermined URLs (based on its default, included wordlist) to see if it gets back anything.
- It will enumerate quite a bit of information about the site's content (esp. directories).

### GoBuster
> GoBuster is a command line tool (-h, --help to see the help menu) similar to DirB (Directory Buster).
- From the [GitHub Repo](https://github.com/OJ/gobuster): Gobuster is a tool used to brute-force...
  - URIs (directories and files) in web sites.
  - DNS subdomains (with wildcard support).
  - Virtual Host names on target web servers.
  - Open Amazon S3 buckets
  - Open Google Cloud buckets
  - TFTP servers
- [Kali's website has usage and examples](https://www.kali.org/tools/gobuster/).

#### General Syntax
```
gobuster [Mode][Options]    # Two required options:
                            #  1. -u : target URL
                            #  2. -w : /path/to/wordlist
```

#### [Modes/Commands](https://github.com/OJ/gobuster?tab=readme-ov-file#modes)
```
dir         # Uses directory/file enumeration mode
dns         # Uses DNS subdomain enumeration mode
fuzz        # Uses fuzzing mode
help        # Help about any command
s3          # Uses aws bucket enumeration mode
version     # Shows the current version
vhost       # Uses VHOST enumeration mode
```
- To find additional help on any of the modes: `gobuster [mode] --help`

#### [DIR Mode/Command](https://github.com/OJ/gobuster?tab=readme-ov-file#dir-mode)
- The DIR mode is used for finding hidden directories and files.
- This is the classic directory brute-forcing mode or Enumerating URIs for directories and files.
- The Dir mode in Gobuster is mainly used to find extra content in a specific target domain or its subdomain. This additional information can include hidden directories or hidden files that can contain sensitive data.

#### DIR Mode Flags/Options
```
-u, --url [string]                     # [required] target URL
-w, --wordlist [string]                # [required] path to the wordlist
-e, --expanded                         # expanded mode, print full URLs
-x, --extensions [string]              # file extension(s) to search for
-b, --status-codes-blacklist [string]  # blacklist status codes
-r, --follow-redirect                  # follow redirects
```

#### Syntax 1: Standard DirBust
```
gobuster dir -u http://[target ip] -w /usr/share/wordlists/dirb/common.txt
```

#### Syntax 2: Blacklist Codes
- Lots of 403 and 404 responses cluttering things up. You can clean that up with:
```
gobuster dir -u http://[target ip] -w /usr/share/wordlists/dirb/common.txt  -b 403,404
```

#### Syntax 3: Find Files
- Look for something specific, like for example PHP or XML files and plaintext files... and follow-redirects:
```
gobuster dir -u http://[target ip] -w /usr/share/wordlists/dirb/common.txt  -b 403,404 -x .php,.xml,.txt -r
```

#### Syntax 4: Specific Directory
- Enumerate the contents of a directory gobuster found (e.g., `/data/`):
```
gobuster dir -u http://[target ip]/data/ -w /usr/share/wordlists/dirb/common.txt  -b 403,404 -x .php,.xml,.txt -r
```

## Nmap Scripts

### Default Scripts
```
nmap -sV -sC -p 80 [target ip]
```

### banner
> A simple banner grabber which connects to an open TCP port and prints out anything sent by the listening service within five seconds. Banner Info is info from the server sent to a client when it first connects (it's basically the simple "API" information necessary for connecting to the server and interacting with it).
```
nmap [target ip] -p 80 --script banner
```

### http-enum
```
nmap -sV -p 80 --script http-enum [target ip]
```
- Similar to dirb. Enumerates directories used by popular web applications and servers.
- Look for "potentially interesting" stuff
- You can use a browser to try to navigate to any of this interesting stuff

### http-headers
```
nmap -sV -p 80 --script http-headers [target ip]
```
- This gives you http header information from the server (and you'll likely get more information about the server, software, version, etc.)
- NOTE: "X-XSS-Protection: 0" means cross-site scripting protection is OFF.
- All this stuff you save in your notes (enumerate) so you can eval for use later

### http-methods
```
nmap -sV -p 80 --script http-methods --script-args http-methods.url-path=/webdav/ [target ip]
# the /webdav/ directory is just an example, but a good one
```
- If you get an interesting directory (e.g., /webdav/) with `http-enum` then plug it in here.
- This will give you a list of methods supported by the server (and some will be useful)
- Enumerate and move on.
- Then check the other interesting directories you found.

### http-webdav-scan
```
nmap -sV -p 80 --script http-webdav-scan --script-args http-methods.url-path=/webdav/ [target ip]
```
- More information on the WebDAV if it's installed and running on the web server.

## MetaSploit

### Modules, List
```
auxiliary/scanner/http/apache_userdir_enum
auxiliary/scanner/http/brute_dirs
auxiliary/scanner/http/dir_scanner
auxiliary/scanner/http/dir_listing
auxiliary/scanner/http/http_put
auxiliary/scanner/http/files_dir
auxiliary/scanner/http/http_login
auxiliary/scanner/http/http_header
auxiliary/scanner/http/http_version
auxiliary/scanner/http/robots_txt 
```

### brute_dirs
> Uses a default standard wordlist and searches the target ip for directories. Similar to `dirb`.
```
> use auxiliary/scanner/http/brute_dirs
> show options
> set [whatever you need to set]
> run
```

### http_version
> O/S, server software, and version info
```
> use auxiliary/scanner/http/http_version
> set rhosts [target ip]
> options
> run
```

## robots.txt
- This file tells search engines which pages it can and cannot index.
- Therefore, it should give you an idea of what the target wants to keep private (those pages it does not want indexed--searchable by public search engines).
- You could probably wget the file if you knew the URL. Or...
- msfconsole:
```
> use auxiliary/scanner/http/robots_txt
> options
> set [whatever]
> run
```

 
