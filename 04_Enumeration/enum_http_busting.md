# Busting
> Busting refers to brute-forcing directories, files, and other information from websites

## Content
- {}()

## Directory Busting

### [DirBuster](https://www.kali.org/tools/dirbuster/)
- Oldie but a goodie. You can still get good results from DirBuster, even when DirB and GoBuster crap out.
- File Extensions for Windows (IIS):
  - asm,asmx,asp,aspx
  - asm,asmx,asp,aspx,txt,zip,rar,bak
- File Extensions for Linux (Apache):
  - php, sh, js, html, py
- NOTE: The longer the list, the longer the search will take.

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

## GoBuster

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
