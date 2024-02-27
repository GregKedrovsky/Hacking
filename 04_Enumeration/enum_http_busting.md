# Busting
> Busting refers to brute-forcing directories, files, and other information from websites

## Content
- [Directory Busting](#directory-busting)
  - [DirBuster](#dirbuster)
  - [DirB](#dirb)
  - [FFUF](#ffuf)
- [DNS Busting](#dns-busting)
  - [Amass](#amass)
    - [Subcommands](#subcommands)
    - [Syntax, enum](#syntax-enum)
  - [Sublist3r](#sublist3r)
    - [Usage](#usage)
    - [Examples](#examples)
  - [DNSRecon](#dnsrecon)
- [GoBuster](#gobuster)
  - [General Syntax](#general-syntax)
  - [Modes](#modes)
  - [DIR Mode](#dir-mode)
    - [DIR Mode Common Flags/Options](#dir-mode-common-flagsoptions)
    - [Syntax 1: Standard Dir Bust](#syntax-1-standard-dir-bust)
    - [Syntax 2: Blacklist Codes](#syntax-2-blacklist-codes)
    - [Syntax 3: Find Files](#syntax-3-find-files)
    - [Syntax 4: Specific Directory](#syntax-4-specific-directory)
  - [DNS Mode](#dns-mode)
    - [DNS Mode Common Flags/Options](#dns-mode-common-flagsoptions)
    - [Syntax: Standard DNS Bust](#syntax-standard-dns-bust)
  - [VHOST Mode](#vhost-mode)
    - [VHOST Mode Common Flags/Options](#vhost-mode-common-flagsoptions)
    - [Syntax: Standard VHOST Bust](#syntax-standard-vhost-bust)

----
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

### [FFUF](https://github.com/ffuf/ffuf)
> Fuzz Faster U Fool (FFUF): A fast web fuzzer written in Go. It's an open source web fuzzing tool, intended for discovering elements and content within web applications, or web servers. At its core, one of the main functions that people use FFUF for, is directory brute forcing.
- Documentation: [wiki](https://github.com/ffuf/ffuf/wiki), [Everything You Need to Know](https://codingo.io/tools/ffuf/bounty/2020/09/17/everything-you-need-to-know-about-ffuf.html), [tutorial](https://medium.com/quiknapp/fuzz-faster-with-ffuf-c18c031fc480)
- The position to be fuzzed should be indicated by the `FUZZ` word in the ffuf command.

#### Use Cases
1. General Directory discovery with option to fuzz at any place in the URL.
2. VHOST discovery without DNS Records
3. Fuzzing using various HTTP methods.

#### Directory and File Discovery

##### Syntax
```
ffuf -w wordlist.txt -u http://website.com/FUZZ
```
- `-w` : wordlist to plug into the position marked with `FUZZ`
- `-u` : URL with `FUZZ` marking the position to plug in words

##### Options
- Ffuf also gives option to get output only of responses with specific status code, amount of lines, response size, amount of words as well as the response which matches a regex pattern.
- A few examples of flags for the same are:
```
-mc                 # to specify Status code
-ml                 # to specify amount of lines in response
-mr                 # to specify regex pattern
-ms                 # to specify response size
-mw                 # to specify amount of words in response
-recursion          # fuzz subdirs below final /FUZZ in URL
-recursion-depth    # specify the depth of recursion
-maxtime [seconds]  # end the ongoing fuzzing after the specified time in seconds
```

##### Examples
```
ffuf -w wordlist.txt -w http://website.com/FUZZ -e .aspx,.html -mc 200,302
# provides output of responses with status code 200 and 302 only
```

#### VHOST Discovery





```
ffuf -w /path/to/subdomain/list -H "Host: FUZZ.[domain name] -u http://[domain name]/ -fc 200
```





ffuf -w subdomains.txt -u http://website.com/ -H “Host: FUZZ.website.com”


----
## DNS Busting

### [Amass](https://github.com/owasp-amass/amass/blob/master/doc/user_guide.md)
> Amass is a powerful open-source reconnaissance tool designed for network mapping and information gathering. It is widely used by security professionals and researchers to map out external network space and discover assets that belong to a target organization.

#### Subcommands
- Amass usage is based on five subcommands (like GoBuster's "modes"):
```
intel : collect open source intelligence for investigation of the target organization
enum  : perform DNS enumeration and network mapping of systems exposed to the Internet
viz   : visualize enumeration results
track : Track differences between enumerations
db    : manage the graph databases storing the enumeration results
```
- To see the available options for a subcommand, just type in the terminal:
```
amass intel
```

#### Syntax, enum
```
amass enum -d thetoppers.htb
```

### [Sublist3r](https://github.com/aboul3la/Sublist3r)
> Sublist3r is a python tool designed to enumerate subdomains of websites using OSINT. It helps penetration testers and bug hunters collect and gather subdomains for the domain they are targeting. Sublist3r enumerates subdomains using many search engines such as Google, Yahoo, Bing, Baidu and Ask. Sublist3r also enumerates subdomains using Netcraft, Virustotal, ThreatCrowd, DNSdumpster and ReverseDNS.

#### [Usage](https://github.com/aboul3la/Sublist3r?tab=readme-ov-file#usage)
```
-d 	--domain 	Domain name to enumerate subdomains of
-b 	--bruteforce 	Enable the subbrute bruteforce module
-p 	--ports 	Scan the found subdomains against specific tcp ports
-v 	--verbose 	Enable the verbose mode and display results in realtime
-t 	--threads 	Number of threads to use for subbrute bruteforce
-e 	--engines 	Specify a comma-separated list of search engines
-o 	--output 	Save the results to text file
-h 	--help 	show the help message and exit
```

#### [Examples](https://github.com/aboul3la/Sublist3r?tab=readme-ov-file#examples)

1. To list all the basic options and switches use -h switch:

```
python sublist3r.py -h
```

2. To enumerate subdomains of specific domain:
```
python sublist3r.py -d example.com
```

3. To enumerate subdomains of specific domain and show the results in realtime:
```
python sublist3r.py -v -d example.com
```

4. To enumerate subdomains and enable the bruteforce module:
```
python sublist3r.py -b -d example.com
```

5. To enumerate subdomains and use specific engines such Google, Yahoo and Virustotal engines
```
python sublist3r.py -e google,yahoo,virustotal -d example.com
```


### [DNSRecon](https://www.kali.org/tools/dnsrecon/)
> DNS Enumeration and Scanning Tool

```
dnsrecon -d example.com -D /usr/share/wordlists/dnsmap.txt -t std --xml dnsrecon.xml

# -d [domain]       # target domain
# -D [dictionary]   # dictionary file of subdomain and hostnames to use for brute force.
# --xml [file.xml]  # XML file to save found records
# -t [type]         # Type of enumeration to perform. Possible types:
                    # std      : SOA, NS, A, AAAA, MX and SRV.
                    # rvl      : Reverse lookup of a given CIDR or IP range.
                    # brt      : Brute force domains and hosts using a given dictionary.
                    # srv      : SRV records.
                    # axfr     : Test all NS servers for a zone transfer.
                    # bing     : Perform Bing search for subdomains and hosts.
                    # yand     : Perform Yandex search for subdomains and hosts.
                    # crt      : Perform crt.sh search for subdomains and hosts.
                    # snoop    : Perform cache snooping against all NS servers for a given domain,
                                 testing all with file containing the domains, file given with -D option.
                    # tld      : Remove the TLD of given domain and test against all TLDs registered in IANA.
                    # zonewalk : Perform a DNSSEC zone walk using NSEC records.
````

----
## [GoBuster](https://github.com/OJ/gobuster)
> Gobuster is a tool used to brute-force: URIs (directories and files) in web sites, DNS subdomains (with wildcard support), Virtual Host names on target web servers, Open Amazon S3 buckets, Open Google Cloud buckets and TFTP servers.
- GoBuster is a command line tool similar to DirB that used to brute-force...
  - URIs (directories and files) in web sites.
  - DNS subdomains (with wildcard support).
  - Virtual Host names on target web servers.
  - Open Amazon S3 buckets
  - Open Google Cloud buckets
  - TFTP servers
- Help is built-in:
  - `gobuster help` : outputs the top-level help.
  - `gobuster help [mode]` : outputs the help specific to that mode.
  - [Kali's website has usage and examples](https://www.kali.org/tools/gobuster/).
  - [Explanation & CheatSheet](https://erev0s.com/blog/gobuster-directory-dns-and-virtual-hosts-bruteforcing/)

### General Syntax
```
gobuster [Mode] [Options]   # Two required options:
                            #  1. -u : target URL
                            #  2. -w : /path/to/wordlist
```

### [Modes](https://github.com/OJ/gobuster?tab=readme-ov-file#modes)
```
dir         # Uses directory/file enumeration mode
dns         # Uses DNS subdomain enumeration mode
fuzz        # Uses fuzzing mode
help        # Help about any command
s3          # Uses aws bucket enumeration mode
version     # Shows the current version
vhost       # Uses VHOST enumeration mode
```
- To find additional help on any of the modes: `gobuster help [mode]`

### [DIR Mode](https://github.com/OJ/gobuster?tab=readme-ov-file#dir-mode)
> This is the classic directory brute-forcing mode or Enumerating URIs for directories and files.
- The DIR mode is used for finding hidden directories and files. 
- The DIR mode in Gobuster is mainly used to find extra content in a specific target domain or its subdomain. This additional information can include hidden directories or hidden files that can contain sensitive data.

#### DIR Mode Common Flags/Options
```
-u, --url [string]                     # [required] target URL
-w, --wordlist [string]                # [required] path to the wordlist
-e, --expanded                         # expanded mode, print full URLs
-x, --extensions [string]              # file extension(s) to search for
-b, --status-codes-blacklist [string]  # blacklist status codes
-r, --follow-redirect                  # follow redirects
```

#### Syntax 1: Standard Dir Bust
```
gobuster dir -u http://[target ip] -w /usr/share/wordlists/dirb/common.txt
```

#### Syntax 2: Blacklist Codes
```
gobuster dir -u http://[target ip] -w /usr/share/wordlists/dirb/common.txt  -b 403,404
# filters out 403 and 404 responses (to remove clutter)
```

#### Syntax 3: Find Files
```
gobuster dir -u http://[target ip] -w /usr/share/wordlists/dirb/common.txt  -b 403,404 -x .php,.xml,.txt -r
# looks for specific files: those ending in .php, .xml, and .txt; and it does so following redirects.

gobuster dir -u mytarget.com -w path/to/my/awesome/wordlist.txt -e -t 100 -x jpg,jpeg,png,gif,ico
# looks for images
```

#### Syntax 4: Specific Directory
```
gobuster dir -u http://[target ip]/data/ -w /usr/share/wordlists/dirb/common.txt  -b 403,404 -x .php,.xml,.txt -r
# enumerates the contents of a directory gobuster found (/data/)
```

### [DNS Mode](https://github.com/OJ/gobuster?tab=readme-ov-file#dns-mode)
> In DNS mode GoBuster attempts to resolve the subdomains of a website via DNS. 

#### DNS Mode Common Flags/Options
```
-d, --domain string        # [required] the target domain
-w, --wordlist [string]    # [required] path to the wordlist
-r, --resolver string      # use custom DNS server (format server.com or server.com:port)
-i, --showips              # sow IP addresses
    --timeout duration     # DNS resolver timeout (default 1s)
    --wildcard             # force continued operation when wildcard found
```

#### Syntax: Standard DNS Bust
```
gobuster dns -d [target domain] -w /path/to/wordlist

gobuster dns -d [target domain] -w /path/to/wordlist -r 10.10.10.10 -i -v
# custom DNS server
```

### [VHOST Mode](https://github.com/OJ/gobuster?tab=readme-ov-file#vhost-mode)
> This mode should not be mistaken to be the same as the DNS mode. In the DNS mode the tool attempts to DNS resolve the subdomains and based on that we are given the result. In vhosts mode the tool is checking if the subdomain exists by visiting the formed url and verifying the IP address.
- If using Gobuster version 3.2.0 and above we also have to add the `--append-domain` flag to our command so that the enumeration takes into account the known vHost (`thetoppers.htb`) and appends it to the words found in the wordlist (e.g., `word.thetoppers.htb`).
- From the help file: Append main domain from URL to words from wordlist. Otherwise the fully qualified domains need to be specified in the wordlist. 

#### VHOST Mode Common Flags/Options
```
-u, --url string             # [required] the target URL
-w, --wordlist [string]      # [required] path to the wordlist
    --append-domain          # [required in v3.2.0 and above] append main domain from URL to words from wordlist
-r, --followredirect         # follow redirects
-H, --headers stringArray    # specify HTTP headers, -H 'Header1: val1' -H 'Header2: val2'
-k, --insecuressl            # skip SSL certificate verification
-P, --password string        # password for Basic Auth
    --timeout duration       # HTTP Timeout (default 10s)
-a, --useragent string       # set the User-Agent string (default "gobuster/3.0.1")
-U, --username string        # username for Basic Auth
```

#### Syntax: Standard VHOST Bust
```
gobuster vhost -u [target domain] -w /path/to/wordlist --append-domain
```

