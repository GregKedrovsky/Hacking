# Enumeration: HTTP

## Contents
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)

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

## Tools & Techniques

### DirB


### GoBuster
> GoBuster is a command line tool (-h, --help to see the help menu) similar to DirB (Directory Buster).
- From the [GitHub Repo](https://github.com/OJ/gobuster): Gobuster is a tool used to brute-force...
  - URIs (directories and files) in web sites.
  - DNS subdomains (with wildcard support).
  - Virtual Host names on target web servers.
  - Open Amazon S3 buckets
  - Open Google Cloud buckets
  - TFTP servers
- [Kali](https://www.kali.org/tools/gobuster/)'s website has usage and examples.
- Syntax 1:
```
gobuster dir -u http://[target ip] -w /usr/share/wordlists/dirb/common.txt
```
- `-u` : URL
- `-w` : wordlist
- Lots of 403 and 404 responses cluttering things up. You can clean that up with:
```
gobuster dir -u http://[target ip] -w /usr/share/wordlists/dirb/common.txt  -b 403,404
```
- `-b` : blacklist (don't display)
- Look for something specific, like for example PHP or XML files and plaintext files... and follow-redirects:
```
gobuster dir -u http://[target ip] -w /usr/share/wordlists/dirb/common.txt  -b 403,404 -x .php,.xml,.txt -r
```
- `-x` : extensions (file extensions to search for)
- `-r` : redirect (follow redirects)
- Enumerate the contents of a directory gobuster found (e.g., `/data/`):
```
gobuster dir -u http://[target ip]/data/ -w /usr/share/wordlists/dirb/common.txt  -b 403,404 -x .php,.xml,.txt -r
```


## Nmap Scripts



