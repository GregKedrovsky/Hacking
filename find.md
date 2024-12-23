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

#### First Argument: Location

The first argument after the `find` command is the location from which you wwant to search.
- Although you may specify a specific directory, you can use a metacharacter to serve as a substitute.
- The three metacharacters that work with this command include:
  - Period (`.`): Specifies the current and all nested folders.
  - Forward Slash (`/`): Specifies the entire filesystem.
  - Tilde (`~`): Specifies the active user's home directory.

#### Example
```
find . -type f -size 1033c ! -executable -exec cat {} \;
```
- Find all files (not directories or links) of 1033 bytes in size that are not executable and which can be read (i.e., you can `cat` the file and read the contents).
- The `-exec` causes find to execute the given command once per file matched
  - It will place the name of the file wherever you put the `{}` placeholder.
  - The command must end with a semicolon, which has to be escaped from the shell, either as `\;` or as `“;”`.
  - [Using the find -exec Command](https://www.baeldung.com/linux/find-exec-command)


### Search Location Shortcuts


### Find by Name
- This is the most common expression


### Find by Type


### Find by Size


### Find by Owner


### Find by Perms


### Find by Timestamps

