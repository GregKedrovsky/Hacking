# [Hashcat](https://www.makeuseof.com/use-hashcat-to-crack-hashes-linux/)

## Step 1: [Hash to File](https://null-byte.wonderhowto.com/how-to/crack-shadow-hashes-after-getting-root-linux-system-0186386/)
- Unlike [John](john_the_ripper.md), the easiest way to use Hashcat is to **_only_** supply the password hashes themselves.
- Copy any hashes (e.g., from the `/etc/shadow` file that you want to crack); copy them into a plain text file.

### Remember the Field Structure: 
- The fields are delimited with a colon (`:`)
- The first field is the username.
- The second field is the encrypted password: 
```
root:$1$/avpfBJ1$x0z8w5UF9Iv./DR9E9Lid.:14747:0:99999:7:::
#    |1|  salt  |        hash          |   other crap    |
```
- Look at the characters following the username "root".
- The `$1$` value indicates the password hashing algorith (ND5 in this case)
- The characters after `$1$`, up to the next `$`, **_are the salt_**.
- In the example above, the SALT is `/avpfBJ1`

### Copy Just the Hashes
- Example: If you have the following accounts with hashes in the /etc/shadow file...
```
root:$1$/avpfBJ1$x0z8w5UF9Iv./DR9E9Lid.:14747:0:99999:7:::
sys:$1$fUX6BPOt$Miyc3UpOzQJqz4s5wFD9l0:14742:0:99999:7:::
klog:$1$f2ZVMS4K$R9XkI.CmLdHhdUE3X9jqP0:14742:0:99999:7:::
msfadmin:$1$XN10Zj2c$Rt/zzCW3mLtUWA.ihZjA5/:14684:0:99999:7:::
postgres:$1$Rw35ik.x$MgQgZUuO5pAoUvfJhfcYe/:14685:0:99999:7:::
user:$1$HESu9xrH$k.o3G93DGoXIiQKkPmUgZ0:14699:0:99999:7:::
service:$1$kR3ue7JZ$7GxELDupr5Ohp6cjZ3Bu//:14715:0:99999:7:::
```
- You would copy out the **_second field_** only and save those values to a text file (e.g. hashes.txt): 
```
$1$/avpfBJ1$x0z8w5UF9Iv./DR9E9Lid.
$1$fUX6BPOt$Miyc3UpOzQJqz4s5wFD9l0
$1$f2ZVMS4K$R9XkI.CmLdHhdUE3X9jqP0
$1$XN10Zj2c$Rt/zzCW3mLtUWA.ihZjA5/
$1$Rw35ik.x$MgQgZUuO5pAoUvfJhfcYe/
$1$HESu9xrH$k.o3G93DGoXIiQKkPmUgZ0
$1$kR3ue7JZ$7GxELDupr5Ohp6cjZ3Bu//
```

## Step 2: Find the Hash Mode
- The most important piece of information here is the hash mode.
- Find it with grep: `hashcat --help | grep SHA512` or `hashcat --help | grep "$6$"`
  - Should be ID 1800
- Or, for MD5: `hashcat --help | grep MD5` or `hashcat --help | grep "$1$"`
  - Should be ID 0500

## Step 3: Crack (Syntax)
```
hashcat [options] hashfile /path/to/wordlist.txt

# Minimum General: 
hashcat -m [value] -a [value] [hashfile] [wordlist]

# For SHA-512:
hashcat -a 3 -m 1800 hashes.txt /usr/share/wordlists/rockyou.txt

# For MD5:
hashcat -a 0 -m 500 -o cracked.txt hashes.txt /usr/share/wordlists/sqlmap.txt

## Output file format code:
hashcat -a 3 -m 1800 -o hashcat_output.txt --outfile-format=2 hash_msf.txt /usr/share/wordlists/rockyou.txt
```
- Note that your wordlist is **_NOT_** an option. Just tag it on the end.
- 

## Options (Summary)
- `-a, --attack-mode=NUM` : Attack-mode, see references below
  - Attack mode NUM:
  - `0` = Straight (default dictionary attack)
  - `1` = Combination (tries different combos of works from your wordlist)
  - `2` = Toggle-Case 
  - `3` = Brute-force (used to generate words matching a specific pattern that is useful when a particular trait such as password length or traits are known.)
  - `4` = Permutation
  - `5` = Table-Lookup
  - `8` = Prince
- `-m, --hash-type=NUM` : Hash-type, see references below
  - Hash types NUM, sample:
  - `0` = MD5
  - `100` = SHA1
  - `900` = MD4
  - `1000` = NTLM
  - `1400` = SHA256
  - `1700` = SHA512
  - `1800` = SHA-512(Unix)
- `-o, --outfile=FILE` : Define outfile for recovered hash
- `--outfile-format=NUM` : Define outfile-format for recovered hash, see references below
  - Outfile formats NUM, sample:
  - `1` = hash[:salt]
  - `2` = plain
  - `3` = hash[:salt]:plain 

## [Show Cracked Passwords](https://hashcat.net/wiki/doku.php?id=frequently_asked_questions#how_can_i_show_previously_cracked_passwords_and_output_them_in_a_specific_format_eg_emailpassword)
- To accomplish this, you need to use the `--show` switch.
- Run the same command you did to crack the passwords, and just add `--show` to the end
```
hashcat -a 0 -m 1800 hashes.txt /usr/share/wordlists/rockyou.txt
hashcat -a 0 -m 1800 hashes.txt /usr/share/wordlists/rockyou.txt --show
```
