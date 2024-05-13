# [John the Ripper](https://www.openwall.com/john/doc/EXAMPLES.shtml)

## Step 1: Unshadow
- If your system is ancient enough that it keeps passwords right in the world-readable /etc/passwd, simply make a copy of that file.
- However, if your system uses shadow passwords, you may use John's "unshadow" utility to obtain the traditional Unix password file: 
```
unshadow /etc/passwd /etc/shadow > unshadowed.txt
```
### Zip File

 Convert ZIP to John Format

John the Ripper requires the password hash to be in a specific format. To convert the ZIP file’s password hash into the appropriate format, use the zip2john utility that comes with John the Ripper. Open a terminal and navigate to the directory containing the ZIP file. Run the following command:

zip2john your_file.zip > zip.hash

This command extracts the password hash from the ZIP file and saves it in a file named zip.hash.

## Step 2: Crack
```
john unshadowed.txt
# this is the default

john unshadowed.txt --wordlist=/usr/share/wordlists/rockyou.txt
# use a custom wordlist to crack

john --format=sha512crypt unshadowed.txt --wordlist=/usr/share/wordlists/rockyou.txt
# use a custom wordlist and specifiy the encryption algorythm ($6$ is SHA-512)
```

## Show Cracked Passwords
```
john --show unshadowed.txt
```
