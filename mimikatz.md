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

## [Mimikatz & Kiwi](https://github.com/gentilkiwi/mimikatz#mimikatz)
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
