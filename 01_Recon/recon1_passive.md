# Passive Recon
**Passive Information Gathering:** gathering info w/o actively engaging the target systems (OSINT).

The more information you have, the more successful you will be during the latter stages of a pentest.

## Target Company's Website

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

## WhoIs 

You use **whois** lookups to find information about a particular domain.
- When the domain was obtained, the owner, the NameServers being used, etc.
- Linux command: `whois`
- Websites, for example: **who.is**
- Test site to play with: [ZoneTransfer.me](zonetransfer.me)
