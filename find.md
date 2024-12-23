# Find Stuff in Linux

There are four basic commands in Linux to find stuff: 
- [whereis](#whereis)
- [which](#which)
- [locate](#locate)
- [find](find)
  - [Basic Syntax](#)
  - [Search Location Shortcuts](#)
  - [Find by Name](#)
  - [Find by Type](#)
  - [Find by Size](#)
  - [Find by Owner](#)
  - [Find by Perms](#)
  - [Find by Timestamps](#)

----
## whereis
> Searches for binary files, source files, and man pages for the specified command names.

### Switches:
- `-b` : searches for binaries
- `-s` : searches for source files
- `-m` :  searches for manuals

### Examples: 
```
whereis ls     # lists binaries, source and manuals if they exist
whereis -b ls  # lists only the binaries
whereis -s ls  # lists only the source files
whereis -m ls  # lists only the man files, if they exist
```

----
## which
> Returns the path to the executable indicated.

Example: If you want to know which version of Python is called when you type in “python” at the command line…
```
which python
```

----
## locate
> Fast way to search for files by name on your disk.

### Update Database
- Important: Update the database before first locate and after significant changes on your disk.
```
updatedb  # as root
```

`locate` will match the pattern you give it. So you may need to pipe to `grep` or provide a more specific search pattern. Example:
```
locate python  # locates all file names that include 'python'
```

----
## find
> The big daddy of all search functions in Linux...

### Resources: 
- [40 Best Examples of Find Command in Linux](https://www.cyberithub.com/find-command-in-linux/)

### Basic Syntax

```
find /path/to/search/ -name searchTerm
 #  1. The first part is the 'find' command itself
 #  2. The second part is where to start searching from
 #  3. The third part is an expression that determines what to find
 #  4. The fourth part is the name of the file/dir/link to find
```

If you get a bunch of garbage on the screen, send `stderr` (by its file descriptor number: 2) to /dev/null:
```
find /path/to/search/ -name searchTerm 2>/dev/null
```

#### Example:
```
find . -type f -size 1033c ! -executable -exec cat {} \;
```
- Find all files (not directories or links) of 1033 bytes in size that are not executable and which can be read (i.e., you can `cat` the file and read the contents).
- The `-exec` causes find to execute the given command once per file matched
  - It will place the name of the file wherever you put the `{}` placeholder.
  - The command must end with a semicolon, which has to be escaped from the shell, either as `\;` or as `“;”`.
  - [Using the find -exec Command](https://www.baeldung.com/linux/find-exec-command)

### Search Location Shortcuts

The first argument after the `find` command is the location from which you want to search.
- Although you may specify a specific directory, you can use a metacharacter to serve as a substitute.
- The three metacharacters that work with this command include:
  - Period (`.`): Specifies the current and all nested folders.
  - Forward Slash (`/`): Specifies the entire filesystem.
  - Tilde (`~`): Specifies the active user's home directory.
- `find` is ***recursive*** by default.

### Find by Name
> This is the most common "expression" (expressions specify how we want to match what we are looking for).

`-name` : searches for the name you provide of a file, directory, or link
```
find / -name file.txt    # specify exact name
find / -name 'file1*'    # use search patterns
find / -name '*.txt'     # use search patterns for extentions
find / -iname 'FILE.txt  # use iname for a case-INsensitive search
```

### Find by Type

Use the `-type` expression. The most common is `f` for "file" but the others are just as easy:

|  Type  | Description       |
|:------:|-------------------|
|   f    | regular file      |
|   d    | directory         |
|   l    | symbolic link     |
|   c    | character device  |
|   b    | block device      |
|   p    | named pipe (FIFO) |
|   s    | socket            |

#### Examples:
```
find . -type d               # find only/all directories, recursive
find . -type f               # find only/all files, recursive
find . -type f -name '*.txt' # find only/all files named with txt extension
```

### Find by Size

Use the `-size` expression.
- All units are rounded up (this is tricky!), for example, 1M = 1048576c but:
- `-size -1M` will find only empty files (because anything less that 1 is 0, therefore this will match only those files less than 1, which is 0M).
- `-size -1048576c` will find files from 0 to 1,048,575 bytes (i.e. 0 to one byte below 1M).

#### Suffix: 
| Suffix | Description                 |
|:------:|-----------------------------|
|   b    | blocks (512 bytes), default |
|   c    | bytes                       |
|   k    | kilobytes                   |
|   M    | Megabytes                   |
|   G    | Gigabytes                   |

#### Prefix
|  Prefix | Description  |
|:-------:|--------------|
|    +    | greater than |
|    -    | less than    |

#### Examples:
```
find . -type f -size +50c  # find all files larger than 50 bytes
find . -type f -size 510c  # find all files exactly 510 byes
```

### Find by Owner

Use the `-user` expression and provide the username or user ID (uid). Example:
```
find . -type f -user greg
```

### Find by Perms

Use the `-perm` expression. 

### perm mode

There are three ways to specify `-perm` mode (see the `find` [man page](https://man7.org/linux/man-pages/man1/find.1.html), perm searches:)): 

|  Prefix  | Description                                         |
|:--------:|-----------------------------------------------------|
|  [none]  | find exact permissions                              |
|    -     | find "at least" permissions (not exact)             |
|    /     | find permission in either owner, group, OR other    |
 
```
-perm mode
 # Finds files with the EXACT match of the perm specified
 # '-perm g=w' or '-perm 0020' would find files that ONLY have the group write permission on and no others.

-perm -mode
 # Finds files with ALL of the perms specified.
 # '-perm -664' would find files that have AT LEAST ALL those perms (e.g., 0777 would be found).
 # '-perm -220' would find files writable by BOTH owner and group.

-perm /mode
 # Finds files with ANY of the perms specified.
 # '-perm /222' would find files writable by somebody (owner, group, AND/OR other).
 # '-perm /220' would find files writable by EITHER owner or group or BOTH.
```

#### Examples:

```
find . -type f -perm 664   # find files with exactly 644 perms in current dir and all subdirs
find . -type f -perm -664  # find files with "at least" 644 perms (and more)
find . -type f -perm /222  # find files writeable by somebody (owner, group, OR other)
find . -type f -perm /220  # find files writeable by EITHER owner OR group (but no one else)
find . -type f -perm -220  # find files writeable by BOTH owner AND group (but no one else)

find / -perm /4000 -type f -exec ls -ld {} \; 2>/dev/null
 # This will find files with the SUID bit set.
 # -l long listing format
 # -d list directory names, not contents
```


#### Find SUID Files

```
find / -perm /4000  # find SUID files (ANY with SUID set)
find / -perm /2000  # find SGID files (ANY with SGID set)
find / -perm /6000  # find (ANY file with ) both SUID and SGID files

# Example Implementations:
find / -perm -u=s -type f 2>/dev/null        # find files with AT LEAST the SUID set
find / -perm -04000 -type f -ls 2>/dev/null  # find files with AT LEAST the SUID set
```

Resource: [Permission Bits](https://www.gnu.org/software/libc/manual/html_node/Permission-Bits.html)

### Find by Timestamps

You can find files by modification, access, or change in timestamps.

**Days:** There are three timestamps for days (24-hour periods): 

|  Type   | Description                                                           |
|:-------:|-----------------------------------------------------------------------|
|  atime  | shows when the file was last accessed (e.g., read)                    |
|  mtime  | shows when the file contents were last modified                       |
|  ctime  | shows when metadata was last changed (including content modification) |

**Minutes:** Three timestamps for minutes (60-second periods):

|  Type  | Description                                                     |
|:------:|-----------------------------------------------------------------|
|  amin  | file was last accessed (e.g., read)                             |
|  mmin  | file contents were last modified                                |
|  cmin  | file metadata was last changed (including content modification) |

|  Prefix  | Description  |
|:--------:|--------------|
|     +    | greater than |
|     -    | less than    |

**Note:** Any fractional part of the time period is ignored. 
- Therefore `-atime +1` will find files accessed TWO days ago or longer (because files accessed today, or within the last 23.99 hours, can be found with `atime 0`). 

#### Examples: 

```
find . -type f -mtime -10
# find all files modified less than 10 days ago
# find all files less than 10 days old

find . -type f -mtime +10
# find all files modified more than 10 days ago
# find all files older than 10 days old

find . -type f -mtime 0
# find all files modified today
```
