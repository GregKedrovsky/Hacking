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

