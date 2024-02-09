# Passive Recon

> Passive Information Gathering: gathering info w/o actively engaging the target systems (OSINT). 

## Contents
- [Techniques](https://github.com/GregKedrovsky/Hacking/blob/main/01_Recon/recon1_passive.md#techniques)
  - [Search Target's Website](https://github.com/GregKedrovsky/Hacking/blob/main/01_Recon/recon1_passive.md#search-targets-website)
  - [WhoIs](https://github.com/GregKedrovsky/Hacking/blob/main/01_Recon/recon1_passive.md#WhoIs)
  - [DNS Recon](https://github.com/GregKedrovsky/Hacking/blob/main/01_Recon/recon1_passive.md#dns-recon)
- [Tools](https://github.com/GregKedrovsky/Hacking/blob/main/01_Recon/recon1_passive.md#Tools)
  - [DNSRecon](https://github.com/GregKedrovsky/Hacking/blob/main/01_Recon/recon1_passive.md#DNSRecon)
  - [DNSDumpster.com](https://github.com/GregKedrovsky/Hacking/blob/main/01_Recon/recon1_passive.md#dnsdumpstercom)
  - [Google Dorks](https://github.com/GregKedrovsky/Hacking/blob/main/01_Recon/recon1_passive.md#google-dorks)
  - [Netcraft](https://github.com/GregKedrovsky/Hacking/blob/main/01_Recon/recon1_passive.md#netcraft)
  - [Sublist3r](https://github.com/GregKedrovsky/Hacking/blob/main/01_Recon/recon1_passive.md#sublist3r)
  - [wafw00f](https://github.com/GregKedrovsky/Hacking/blob/main/01_Recon/recon1_passive.md#wafw00f-kali-page)

## Techniques

### Search Target's Website

What we are looking for when we gather information on a website: 
- IP Addresses
- Directories hidden from search engines
- Names
- Email Addresses
- Phone Numbers
- Physical Addresses
- Web technologies in use

1. Find the website URL and then get the IP with the `host` command.
    - Example: `host hackersploit.org`
    - If you get more than one IP, then you are dealing with some sort of proxy (like CloudFlare)
    - You could also get IPv4 and IPv6 addresses and the mail server
2. Scroll through the website, find social media links. Make note of those
3. See if the site has a `robots.txt` file. 
    - Just include `/robots.txt` after the valid URL
    - This file exists to tell search engines like Google what files can and cannot be indexed
    - If they say it cannot be indexed... They're hiding something and you can get an idea of what
4. See if the site has a `sitemap.xml` or `sitemaps.xml` file.
    - This file is generally used to provide search engines with information
5. Browser plugins that are helpful: 
    - **BuiltWith**: Technology profiler. Tells you what tech is running on a website (and subdomains, too).
    - **WappAlyzer**: Technology profiler
6. Kali tool: `whatweb` (run `whatweb` with no options to get usage help)
7. Download the whole website with **HTTrack** (from httrack.com)

### WhoIs 

You use **whois** lookups to find information about a particular domain.
- When the domain was obtained, the owner, the NameServers being used, etc.
- Linux command: `whois`
- Websites, for example: **who.is**
- Test site to play with: [ZoneTransfer.me](https://digi.ninja/projects/zonetransferme.php)

### Netcraft: Website Footprinting

**Netcraft:** another website passive recon tool... Site used to gather information about a target domain.
https://www.netcraft.com/
https://www.netcraft.com/tools/
https://sitereport.netcraft.com/  (What's that site running?)

You get most of the same stuff as a **whois** but very prettified. 
- It also provides other goodies like SSL/TLS information (when a certificate expires... that might important).
- It will also give you information on SSL vulnerabilities a site might have. 

### DNS Recon

Still passive recon therefore we are not interacting directly with the DNS server. 

Find the DNS records associated with a particular domain. Info like: 
- A Records (IPv4)
- AAAA Records (IPv6)
- NameServers
- Mail Server

## Tools

### [DNSRecon](https://www.kali.org/tools/dnsrecon/)
- DNSRecon is a Python script that provides the ability to perform:
  - Check all NS Records for Zone Transfers.
  - Enumerate General DNS Records for a given Domain (MX, SOA, NS, A, AAAA, SPF and TXT).
  - Perform common SRV Record Enumeration.
  - Top Level Domain (TLD) Expansion.
  - Check for Wildcard Resolution.
  - Brute Force subdomain and host A and AAAA records given a domain and a wordlist.
  - Perform a PTR Record lookup for a given IP Range or CIDR.
  - Check a DNS Server Cached records for A, AAAA and CNAME
  - Records provided a list of host records in a text file to check.
  - Enumerate Hosts and Subdomains using Google

```
dnsrecon --help               # for usage and help
dnsrecon -d hackersploit.org  # example
```

### [DNSDumpster.com](https://dnsdumpster.com/) 
> DNS Recon & Research. Find & Lookup DNS Records
- DNSdumpster.com is a FREE domain research tool that can discover hosts related to a domain. Finding visible hosts from the attackers perspective is an important part of the security assessment process.
- Type in your domain into the search box.
- Hover over icons in search results to see if active or passive, and what it does.

### [Google Dorks](https://www.googleguide.com/advanced_operators_reference.html)
> Also called "Google Hacking." Mainly refers to pulling sensitive information from Google using advanced search terms that help users search the index of a specific website, specific file type, and some exciting information from unsecured Websites.
- [GHDB](https://www.exploit-db.com/google-hacking-database) | [Cheat Sheet](https://gist.github.com/sundowndev/283efaddbcf896ab405488330d1bbc06) | [1000 Best List](https://gbhackers.com/latest-google-dorks-list)
- Use specific filters in the Google search box (followed by a colon, no space, then term provided)
- Examples:
  - Find subdomains: `site:*.ine.com`
  - Find subdomains related to admins: `site:*.ine.com inurl:admin` OR  `site:*.ine.com intitle:admin`
  - Find pdfs one a site: `site:ine.com filetype:pdf`
  - Search for a keyword (e.g., employees) on a specific site: `site:ine.com employees`
  - Check for directory listings: `intitle:"index of"`
  - Search for directories with exposed passwords: `inurl:auth_user_file.txt`  OR  `inurl:password.txt` (or passwd.txt, etc-passwd.txt)
  - WordPress config files: `inurl:wp-config.bak`

### [Leaked Password Databases](https://www.google.com/search?q=Leaked+Password+Databases)
> Objective: Find a leaked password for a user and do a "password spraying" attack to see if he used the same password for multiple sites.
- Site Example: [Have I Been Pwned](https://haveibeenpwned.com/). Aggregator for most all data breach sets.
- After you get a bunch of target employee email addresses, you just plug them into this search one by one to see if they have been compromised. 

### [Netcraft](https://www.netcraft.com/)
> Website Footprinting. Passive recon tool that can be used to gather information about a target domain. And it presents that information in a very prettified mode.
- [Site](https://www.netcraft.com/) | [Tools](https://www.netcraft.com/tools/) | [Site Report](https://sitereport.netcraft.com/) (What's this site running?)
- You get most of the same stuff as a `whois` but it also provides other goodies like SSL/TLS information (when a certificate expires... that might important). It will also give you information on SSL vulnerabilities a site might have. 

### [Sublist3r](https://github.com/aboul3la/Sublist3r)
> Sublist3r is a python tool designed to enumerate subdomains of websites using OSINT. It helps penetration testers and bug hunters collect and gather subdomains for the domain they are targeting. Sublist3r enumerates subdomains using many search engines such as Google, Yahoo, Bing, Baidu and Ask. Sublist3r also enumerates subdomains using Netcraft, Virustotal, ThreatCrowd, DNSdumpster and ReverseDNS.
- Still a passive recon. Kali tool. It basically automates [Google Dorks](stillNeedLink).
- You CAN use this tool to brute-force information (`subbrute`), but that would be an **active** search.
- Using the tool's passive search capacity, you utilize publicly available search engines to gather information.
- [Usage & Examples](https://github.com/aboul3la/Sublist3r#usage) | [Examples from Kali](https://www.kali.org/tools/sublist3r/)

### [theHarvester](https://github.com/laramies/theHarvester)
> theHarvester is a tool for gathering subdomain names, e-mail addresses, virtual hosts, open ports/ banners, and employee names from different public sources (search engines, pgp key servers). It is similar to Sublist3r (gather subdomains, etc.).
- [Kali Info Page](https://www.kali.org/tools/theharvester/)
- You can then use harvested emails for phishing, malicious attachments, usernames for brute-forcing, etc. 

### [wafw00f](https://github.com/EnableSecurity/wafw00f) ([Kali Page](https://www.kali.org/tools/wafw00f/))
> WAF = Web Application Firewall
- Detect & Fingerprint web application firewalls (WAFs)
- Sends a *normal* HTTP request and analyses the response; this identifies a number of WAF solutions.
- If that is not successful, it sends a number of (potentially malicious) HTTP requests and uses simple logic to deduce which WAF it is.
- If that is also not successful, it analyses the responses previously returned and uses another simple algorithm to guess if a WAF or security solution is actively responding to our attacks.

```
wafw00f -h                       # for help
wafw00f -a [sitename with or without https]
```






